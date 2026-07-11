---
title: "Steam Deck"
author: "Graham Plata"
date: 2022-04-16
description: "A catch-all for everything Steam Deck"
tags: ["guide", "gaming", "steam-deck"]
draft: false
---

## Overview

A place for my findings and useful gists for the **Valve** _Steam Deck_.

## Table of Contents

- [Guides](#guides)
- [Table of Contents](#table-of-contents)
- [Parsec](#parsec)
- [EmuDeck](#emudeck)
  - [Resources](#resources)
- [Xbox Cloud Gaming](#xbox-cloud-gaming)
  - [Installing Edge Browser](#installing-edge-browser)

## Parsec

> A remote desktop application for low-latency gaming.

## EmuDeck

> Play all retro games.

1. [Download the installer](https://www.emudeck.com/EmuDeck.desktop) below, copy it to your Deck's Desktop, and run it.

```bash
wget -q https://www.emudeck.com/EmuDeck.desktop
```

1. Now close Steam and run Steam ROM Manager when asked by the app.
2. Click on Preview, then Generate App list, wait for all the images to download, and then click Save App list.
   > The first run may take a few minutes. Check the Event Log tab to see when the process is finished.
3. Close Steam ROM Manager and the installer window. Click on "Return to game mode" on your desktop, and you're good to go!

## Resources

- [EmuDeck](https://www.emudeck.com/) – EmuDeck automates the installation of emulators from the same sources you'll use if done manually.

## Xbox Cloud Gaming

> Installing "Xbox Cloud Gaming (Beta)"

## Installing Edge Browser

Launch the Steam Deck into Desktop Mode from the **STEAM** button menu.

```shell
Power > Switch to Desktop
```

Find **Microsoft Edge (beta)** in the Discover Software Center application and install.

Upon completion, open the app drawer, locate Microsoft Edge (beta), then right-click and Add to Steam. A window should appear, scroll to find Microsoft Edge (beta), check the box next to it, and select Add Selected Programs.

Launch a fresh terminal window (Konsole) and execute the command below.

```bash
# The following command allows Edge to communicate with the Steam Deck's controller interfaces
flatpak --user override --filesystem=/run/udev:ro com.microsoft.Edge
```

Open Steam from the desktop and navigate to non-Steam applications. Right-click Edge and change its name to "Xbox Cloud Gaming (Beta)". Next, right-click again and adjust the shortcut launch options.

```bash
# Append the options to the end of the existing command (after @@u @@)
--window-size=1024,640 --force-device-scale-factor=1.25 --device-scale-factor=1.25 --kiosk "https://www.xbox.com/play"
```

The next steps are optional; use them if you want to add custom artwork.

```bash
# Download Microsoft assets
wget -q --show-progress https://aka.ms/EdgeXboxDeckArt
```

In Desktop Mode on your Steam Deck, launch Steam, navigate to Xbox Cloud Gaming (Beta) in your Library, right-click it, select Properties, and select the appropriate images.

- [support.microsoft.com](https://support.microsoft.com/en-gb/topic/xbox-cloud-gaming-in-microsoft-edge-with-steam-deck-43dd011b-0ce8-4810-8302-965be6d53296) – Xbox Cloud Gaming in Microsoft Edge with Steam Deck
- [flatpak](https://docs.flatpak.org/en/latest/introduction.html) – Flatpak is a framework for distributing desktop applications across various Linux distributions.
