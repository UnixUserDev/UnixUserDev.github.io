---
tags:
  - memory/system
created: 2026-10-21T21:49:18
source: agent
---

The SpaceCachyOS desktop is deliberately Wayland-only KDE Plasma (startplasma-wayland only, no kwin-x11 / plasma-x11-session / xorg-server; /usr/share/xsessions does not exist). GPU is an RTX 4060 on the nvidia-open drivers with nvidia_drm modeset and fbdev enabled. Login goes through Plasma Login Manager (plasmalogin) with session "plasma". xorg-xwayland stays installed because plasma-workspace and gamescope depend on it.

[[Inbox]]
