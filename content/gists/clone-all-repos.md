---
title: "Clone All Repos"
author: "Graham Plata"
date: 2026-05-22T09:49:09-04:00
type: "gist"
language: "bash"
description: "Clone all repositories from a GitHub organization"
tags: ["github", "bash", "automation"]
draft: false
---

## Overview

Clones all repositories from a GitHub organization to the current directory, skipping any that already exist locally.

## Code

```bash
#!/bin/bash

set -euo pipefail

if [ $# -ne 1 ]; then
    echo "Usage: $0 <org-or-user>"
    echo "Example: $0 grahamplata"
    exit 1
fi

org="$1"
limit=1000
count=0

echo "Cloning repositories from '$org'..."

gh repo list "$org" --limit "$limit" | while read -r repo _; do
    repo_name="${repo##*/}"
    if [ ! -d "$repo_name" ]; then
        echo "Cloning $repo_name..."
        gh repo clone "$repo" "$repo_name"
        ((count++))
    else
        echo "Skipping $repo_name (already exists)"
    fi
done

echo "Done!"
```

## Usage

```bash
chmod +x clone-all-repos.sh
./clone-all-repos.sh grahamplata
```

Or for a user account:

```bash
./clone-all-repos.sh username
```

## Notes

- Requires `gh` CLI to be installed and authenticated
- The `--limit 1000` handles orgs with up to 1000 repos
- Parameter expansion `${repo##*/}` extracts just the repo name from the full path
- Skips cloning if directory already exists to avoid overwriting local work
