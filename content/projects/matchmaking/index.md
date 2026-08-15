---
title: "Goldilocks – Building a Game Matchmaking Engine"
author: "Graham Plata"
date: 2026-07-25
type: "project"
description: "Building a resilient 1v1 matchmaking service with Go, Connect RPC, and Redis"
tags: ["code", "games", "go", "distributed-systems"]
draft: true
---

Matchmaking looks simple from the outside: a player presses **Play**, waits for a moment, and lands in a game. _(Dota for me)_ Behind the button press is a system balancing several competing goals. Matches should be fair, wait times should be short, and never place the same player into different games.

[Goldilocks](https://github.com/grahamplata/goldilocks) is my attempt to build that system from first principles. It is a 1v1 matchmaking service written in Go using Connect RPC and Redis. The name comes from the central tension in matchmaking: the acceptable skill range cannot be too narrow or too broad. It has to be _just right_.

## Start with one match

I am approaching this intentionally constrained. A match has two players. Each player queues for one game mode and region with a matchmaking rating (MMR) and measured ping. The service must find a compatible opponent, provision a game server, and send the result to the player's clients.

The resulting flow is:

```diagram
┌────────┐  ConnectRPC   ┌─────────┐   notify   ┌────────────┐
│ Client │──────────────▶│ Handler │───────────▶│ Matchmaker │
└───▲────┘               └────┬────┘            └─────┬──────┘
    │ MatchFound              │                       │
    │                         ▼                       ▼
    │                    ┌─────────┐           ┌─────────────┐
    └────────────────────│  Redis  │◀──────────│ Provisioner │
                         └─────────┘           └─────────────┘
```

The handler is in charge of the client session, matchmaker pairing, Redis owns coordination, and the provisioner allocates a game server.

## One stream for the queue lifecycle

We expose a bidirectional streaming RPC named `Queue`. The first client message is a join request containing: `player id`, `mmr`, `mode`, `region`, `ping`, and an optional `rejoin token`.

The server acknowledges the join with a session token. The client then keeps the stream alive with heartbeats while it waits. The same stream carries cancellation requests in one direction and the eventual `MatchFound` event in the other.

```diagram
┌────────┐                              ┌─────────────┐
│ Client │                              │ Goldilocks  │
└───┬────┘                              └──────┬──────┘
    │ Join(player, MMR, mode, region)          │
    │─────────────────────────────────────────▶│
    │ Queued(session token)                    │
    │◀─────────────────────────────────────────│
    │ Heartbeat                                │
    │─────────────────────────────────────────▶│
    │ MatchFound(match, server, opponent)      │
    │◀─────────────────────────────────────────│
```

## Redis is the coordination layer

Players are partitioned by game mode and region. Each partition is a Redis sorted set:

```text
queue:<game-mode>:<region>
```

The player ID is the member and MMR is the score. This keeps incompatible players out of the same search and gives the matchmaker an efficient, ordered pool. A separate Redis hash stores the rest of the queue entry:

```text
queue-meta:<player-id>
├── MMR
├── game mode
├── region
├── ping
├── joined at
├── attachment ID
└── state
```

The queue entry has a ttl that is refreshed by heartbeats. If metadata expires while a player ID remains in a sorted set, we remove the orphaned member on its next scan.

## The expanding window

A strict MMR limit creates good matches when the queue is busy, but it can leave players waiting forever when the population is thin. Immediately accepting a wide range solves the wait but creates unnecessarily uneven games.

Goldilocks uses an expanding-window strategy. A player starts with a ±50 MMR radius. Every ten seconds the radius grows by 25, up to a maximum of ±200.

```text
window = min(50 + floor(wait time / 10s) × 25, 200)
```

The matchmaker scans a player's partition and chooses the closest opponent inside that player's current window, provided the opponent is also below the 150 ms ping ceiling.

The useful design choice is that the window is not stored. It is a pure function of `JoinedAt` and the current time. A restarted engine or another replica derives the same radius without synchronizing mutable expansion state.

The engine scans immediately when a player joins and scans every partition again on a ticker. The immediate path keeps the common case responsive; the ticker gives waiting players another chance after their windows expand. Join notifications are deliberately best-effort—if the local channel is full, Redis still contains the player and the next periodic scan will find them.

## Claiming a pair exactly once

Selecting two compatible players is the easy part. Owning that decision is harder.

Imagine two engine replicas scanning the same partition:

```text
engine A reads players 1 and 2
engine B reads players 1 and 2
engine A chooses the pair
engine B chooses the pair
```

If selection and removal are separate operations, both engines can provision a server for the same players. Goldilocks resolves the race with `ClaimPair`, a Redis Lua script that checks both players are queued, removes both from the sorted set, marks both as claimed, and records the claim ID as one atomic transition.

```text
queued + queued ── atomic ClaimPair ──▶ claimed + claimed
```

Only one caller can win. A competing matchmaker, cancellation, or disconnect reaper sees that queue membership has already changed and loses without needing to undo anything. The engine itself holds no cross-goroutine coordination state, which allows replicas to race safely through Redis.

This property has a dedicated test that runs two engines against the same pair repeatedly and asserts that exactly one server is provisioned each time. Concurrency behavior deserves a test at the invariant boundary, not only a collection of single-threaded unit tests.

## Provision, finalize, then publish

Winning a claim begins match formation; it does not complete it. The engine then:

1. Provisions a game server using the claim ID as the match ID.
2. Builds a result for each player containing the match, server, and opponent.
3. Atomically stores both results and finalizes the claim.
4. Publishes a notification to each player's Redis channel.

Persisting before publishing closes an important gap. Redis Pub/Sub is ephemeral: a disconnected client will miss a notification. The stored result is retained for five minutes, so a reconnecting player can receive a match that formed while they were offline.

The provisioner is an interface rather than part of the matching algorithm:

```go
type Provisioner interface {
    Provision(ctx context.Context, match MatchResult) (ServerInfo, error)
    Deprovision(ctx context.Context, matchID string) error
}
```

Local development currently uses a mock that returns `127.0.0.1:7777`. A Kubernetes provisioner is stubbed out as the next infrastructure step. Keeping that boundary narrow lets queue and failure behavior be tested without a cluster.

## Failure needs a path back to queued

Provisioning is an external side effect. It can fail cleanly, time out before doing anything, or succeed while its response is lost. Goldilocks treats all provisioning errors as potentially ambiguous.

After a failure, the engine attempts to deprovision using the match ID and then rolls both players back into the queue. `Deprovision` must be idempotent so that cleanup is safe even when the server was never created or has already been removed.

```diagram
┌────────┐   claim    ┌─────────┐   provision    ┌─────────┐
│ Queued │───────────▶│ Claimed │───────────────▶│ Matched │
└────────┘            └────┬────┘                └─────────┘
    ▲                      │
    └──────────────────────┘
       cleanup + rollback
```

There is still one worse case: the engine can crash after claiming players or allocating a server. Claimed players are indexed by claim time. On each tick, another engine can find claims older than the formation timeout, atomically acquire recovery ownership, deprovision by claim ID, and return the players to the queue.

Recovery ownership matters because a slow original worker could wake up while another replica is cleaning up. Moving entries from `claimed` to `recovering` fences the original worker from finalizing the same claim.

## Surviving client disconnects

A dropped connection should not immediately throw away a player's place. Mobile networks change, Wi-Fi blips, processes restart, and load balancers close streams.

On a fresh join, Goldilocks uses `SET NX` to create a session token. The token is both a reconnect credential and an atomic mutex preventing two fresh streams from queuing the same player. A reconnect presents that token and resumes the existing queue entry without changing `JoinedAt`, so the player keeps both their place and their expanded MMR window.

Every stream also receives a unique attachment ID. Disconnect handling only updates the queue entry when that attachment ID is still current. This fences an old stream from marking a newer replacement stream as disconnected.

When a stream disappears, the player remains matchable during a 30-second grace period:

- If the player reconnects, the disconnect marker is cleared.
- If a match forms, the result is buffered for reconnect.
- If the grace period expires, the engine removes the player.
- If the client explicitly cancels, removal is immediate.

This is the part of the project that made the queue feel like a distributed system rather than an algorithm exercise. Most of the complexity lives in transitions between joined, attached, disconnected, claimed, recovering, and finalized—not in comparing two MMR values.

## Running it locally

Redis and RedisInsight are included in the Docker Compose configuration. With Redis running, the server and simulator can be started with:

```bash
docker compose up -d redis
go build -o goldilocks ./cli
./goldilocks server start
```

In another terminal:

```bash
./goldilocks simulate --players 6
```

The simulator creates fake clients with incrementing MMR values. Each opens a queue stream, sends heartbeats, waits for a match, and reports its opponent, server address, and total wait.
