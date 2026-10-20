---
tags:
  - memory/tech
created: 2026-10-20T09:23:56
source: agent
---

On the CachyOS KDE Plasma 6.8 Wayland desktop, Spectacle is disliked/abandoned as the screen capture tool. Screenshots are handled by Flameshot 14 (Print key, region snip with annotate toolbar, saves to ~/Pictures/Screenshots as "Shot <date> <time>.png"), and video recording by Kooha 2.3.2 (flatpak, because pkexec/pacman install of kooha was refused; hotkey Meta+Shift+R). Game clips use the existing Vice replay-buffer daemon which runs gpu-screen-recorder with a 300s buffer. gpu-screen-recorder-ui was installed but is broken on KDE Wayland (shows transparent window and a Wayland warning), so it is not used.

[[Inbox]]
