---
tags:
  - memory/gaming
created: 2026-10-17T19:09:48
source: agent
---

On this machine (KDE Plasma 6.8 / Wayland / KWin XWayland), Brick Rigs (Steam appid 552100) breaks when launched through gamescope: mouse-look camera seizes up and input drifts constantly toward the bottom-right. Removing gamescope from the launch options entirely fixes it — the game runs fine directly under KWin's XWayland, where pointer warps work properly. Tested and confirmed by the user; gamescope made it worse, not better.

[[Inbox]]
