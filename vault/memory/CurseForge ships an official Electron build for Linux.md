---
tags:
  - memory/linux
created: 2026-10-20T13:49:19
source: agent
---

The CurseForge support docs still say "Windows and macOS only", but Overwolf does publish a first-party Linux Electron client at https://curseforge.overwolf.com/electron/linux/CurseForge_<ver>_amd64.deb (deb only, no rpm/AppImage). On Arch/CachyOS the AUR package `curseforge` (maintainer txtsd) is not a Wine wrapper — its PKGBUILD just repackages that official .deb into /opt/curseforge. Installed that way it is pacman/AUR tracked, so `yay -Sua` updates it. Verified working on CachyOS X11: launches to the CurseForge SSO login screen. No system Java present, so Minecraft instances need a JRE (or the app's managed Java) separately.

[[Inbox]]

[[Inbox]]
