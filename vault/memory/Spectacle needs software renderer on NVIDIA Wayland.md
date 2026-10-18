---
tags:
  - memory/linux
created: 2026-10-18T04:47:23
source: agent
---

On SpaceCachyOS (KDE/Wayland, NVIDIA RTX 4060, driver 615.78.08), Spectacle 6.8.0 segfaulted on every launch (SIGSEGV) with the crash thread QSGRenderThread inside libnvidia-eglcore via QRhi::endFrame — Qt's QML window rendering through the EGL/GL path. It looked like "won't open" but it was crashing in ~1s. Fix: force QtQuick software rendering for Spectacle only via systemd user drop-in `~/.config/systemd/user/app-org.kde.spectacle.service.d/override.conf` with `Environment=QT_QUICK_BACKEND=software` (because the .desktop is DBusActivatable, all menu/hotkey launches go through that unit). Also aliased `spectacle` in ~/.bashrc for CLI. Force X11 (QT_QPA_PLATFORM=xcb) also works but is heavier. Real long-term fix = Qt or NVIDIA driver update.

[[Inbox]]
