---
title: "Tiny tools for small problems"
date: 2026-11-23
draft: false
tags: ["macos", "swift", "tools"]
---

I use a [Logitech PRO X2 SUPERSTRIKE](https://www.logitechg.com/en-us/shop/p/pro-x2-superstrike-mouse.910-007700) mouse, but I love natural scrolling on the trackpad. The problem: macOS only has one global "natural scrolling" setting for all devices. Flip it on, and the trackpad feels right but the mouse scrolls backwards. Flip it off, and it's reversed. Every time I sat down at my desk, I was digging into System Settings to toggle it, then undoing it later.

So I built [ScrollSwitch](https://github.com/grahamplata/scrollswitch): a tiny menu bar app that lives as a single arrow icon. Left-click flips scroll direction instantly, right-click quits. It taps into the raw scroll-wheel event stream and inverts it on the fly, no need to touch System Settings at all.

It's deliberately dumb, it doesn't try to detect which device is scrolling, it just gives you one click to flip the global switch instead of four clicks through Settings. The only real friction is macOS asking for Accessibility and Input Monitoring permissions, since reading system-wide scroll events requires that level of access.

Small app, small annoyance, but it's saved me a daily trip through Settings I was tired of making.

## References

- [Logitech PRO X2 SUPERSTRIKE](https://www.logitechg.com/en-us/shop/p/pro-x2-superstrike-mouse.910-007700)
- [grahamplata/scrollswitch on GitHub](https://github.com/grahamplata/scrollswitch)
