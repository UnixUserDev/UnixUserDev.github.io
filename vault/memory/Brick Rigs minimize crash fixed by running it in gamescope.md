---
tags:
  - memory/linux
created: 2026-10-17T03:26:06
source: agent
---

Brick Rigs (Steam appid 552100, native UE4.27 Linux Vulkan build) crashed every time its window was minimized on this machine. Root cause: the game runs as an X11 client under Xwayland (KDE Plasma 6.8 Wayland session, NVIDIA open driver 615.78.08, RTX 4060). Unmapping the X11 window wedges UE4's Vulkan present path; the NVIDIA driver's watchdog fires `NVRM: Xid 109 (CTX SWITCH TIMEOUT)` and kills the context, giving `VK_ERROR_DEVICE_LOST` and a GPUCrash dump (6 identical dumps in ~/.config/Epic/BrickRigs/SavedRemastered/Crashes). Windowed, borderless-fullscreen and resized windows all crashed identically, so no in-game setting fixes it.

Fix applied: installed gamescope and set the Steam launch option to `gamescope -W 1920 -H 1080 -r 120 -f -- %command%`. Verified: launched through Steam, minimized and restored the gamescope window via a KWin script — game survived, zero Xid 109, no new crash dump, still rendering at 60% GPU util. Undo by clearing the launch option in Steam properties.

[[Inbox]]
