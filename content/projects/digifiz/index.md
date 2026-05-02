---
title: "Digifiz: Recreating the Legendary VW Digital Dashboard"
author: "Graham Plata"
date: 2026-05-01
type: "project"
project_type: "software"
description: "Open-source recreation of the iconic 1980s Volkswagen DigiFiz digital instrument cluster for modern vehicles"
tags: ["volkswagen", "automotive", "retro-futurism", "open-source", "dashboard"]
draft: false
---

## Overview

{{< figure src="cluster.png" alt="DigiFiz digital dashboard" caption="The iconic DigiFiz design, recreated in software" >}}

An open-source homage to the legendary VW DigiFiz—the factory-optional all-digital instrument cluster for Golf MK2 and Jetta MK2 (1986-1992). Bringing cutting-edge 80s futurism to modern vehicles.

## The Story

In the mid-1980s, Volkswagen offered something extraordinary: the DigiFiz—a factory-optional all-digital instrument cluster for the Golf MK2 and Jetta MK2. Manufactured by VDO between 1986 and 1992, this retro-futuristic display represented the cutting edge of automotive electronics. Today, original units are rare, collectible, and hoarded by enthusiasts who understand what they have.

The DigiFiz wasn't just functional—it was a design statement. Bold digital typography, segmented displays, and that unmistakable 80s futurism made it instantly recognizable. Decades later, its aesthetic still turns heads.

Now you can recreate that magic without the rarity, the cost, or the fragility of original hardware.

## Key Features

- **Segmented LED Typography** – Distinctive digital readout feeling with that authentic 80s aesthetic
- **Minimal, Purposeful Layout** – Speed, RPM, fuel, warnings—nothing else. Optimized for quick glances at highway speeds
- **High Contrast Display** – No-nonsense presentation with premium, exclusive feel
- **Scene-Based Architecture** – Menu systems, splash screens, dashboard transitions with fade effects
- **Component Composition** – Add gauges, warning lights, and displays by assembling building blocks
- **Pluggable Input** – Support CAN-Bus (real vehicle data), keyboard (testing), or autonomous control modes
- **Data-Driven Design** – Swap images and layouts without recompiling

### Example Usage

Explore the [repository](https://github.com/grahamplata/digifiz) for the complete architecture, component system, and contribution guidelines.

```bash
# Run with the iconic dashboard
go run ./cli start --skip-menu

# Test with keyboard controls
go run ./cli start --demo

# See real-time telemetry
go run ./cli start --debug
```

## Architecture & Design

Built on **Ebiten**, a 2D game library for Go, Digifiz leverages a complete rendering and input loop designed for games. This unconventional choice brings powerful benefits: smooth animations, efficient screen updates, scene transitions with fade effects, and a robust event system—all optimized for real-time graphics. The scene-based architecture treats the dashboard as a state machine, allowing seamless transitions between menu, splash, and dashboard screens without the complexity of traditional UI frameworks.

Component composition means you're never locked into a specific gauge or layout—swap, customize, and extend with ease. Dashboard scenes, lighting systems, and gauges all implement a unified `Component` interface, making it straightforward to add new visual elements.

### Target Hardware

Digifiz is designed for the **Waveshare 12.3" 1920x720 LCD display**—a landscape ultrawide display perfectly proportioned for an instrument cluster. At 1920×720 pixels, it provides the same aspect ratio as classic multi-gauge dashboards while delivering modern clarity and color depth. Whether integrated into a classic restoration or a modern EV build, this display brings the DigiFiz aesthetic to life with sharp, responsive rendering.

## Coding Patterns & Design

Digifiz demonstrates several clean software patterns that keep the codebase maintainable and extensible:

### Component-Based Architecture

The visual layer uses composition through a unified `Component` interface (`internal/components/components.go`):

```text
Component ├── Updatable (Update())
          └── Drawable (Draw())
```

All visual elements—gauges, lights, text, backgrounds—implement this interface. A `BaseComponent` provides common functionality (position, visibility, active state), allowing new components to compose rather than inherit.

**Result**: Adding a new gauge or warning light is just implementing `Update()` and `Draw()`, then registering it with the Dashboard. No deep hierarchies, no tight coupling.

### Strategy Pattern for Input

The input system (`pkg/input/`) abstracts input sources behind an `InputSource` interface:

- `KeyboardInput` – for demo/testing
- `CANBusInput` – for real vehicle data
- `NoOpInput` – for autonomous modes

Each implementation handles its own protocol. Swap input modes via CLI flags without touching dashboard logic.

### Scene State Machine

Scenes (`internal/scene/scene.go`) implement a clean lifecycle:

```go
OnEnter(ctx) → Update() → Draw() → OnExit()
```

Scenes manage their own state and receive shared context (vehicle data, config) on entry. Transitions include fade effects handled by the SceneManager. This pattern makes menu → splash → dashboard flows explicit and testable.

### Pointer Nullability for Optional Values

`InputData` uses pointers (`*int`, `*bool`) to distinguish between "no update" (nil) and "update to zero/false"—avoiding ambiguity when parsing sparse input from keyboards or CAN bus.

## The Hackable Dashboard

Digifiz's true power emerges when you connect it to real vehicle data. The architecture is intentionally designed for this:

- **CAN Bus Integration** – Plug in a `CANBusInput` source to read live vehicle telemetry. No dashboard code changes needed.
- **Visual Mapping** – Define how CAN values (engine temp, oil pressure, battery voltage) map to gauge positions, light states, and text displays. Data-driven, not hardcoded.
- **Scene Flexibility** – Build custom dashboard layouts, menu systems, and warning displays. Scenes are just Go code implementing a simple interface.
- **Asset Swapping** – Swap fonts, images, and color schemes without recompiling. Point to different asset directories or load from disk.

**Future directions:**

- Live CAN logging and replay for testing without a vehicle
- Web-based dashboard configurator – drag gauges, set CAN mappings, preview in real-time
- Multi-screen support – secondary displays for cluster info or media controls
- OBD-II and ELM327 integration for older vehicles without native CAN
- Community component library – share custom gauges, effects, and themes

The goal: a platform where any enthusiast can wire up a dashboard to their car, customize every pixel, and own their instrument cluster experience.
