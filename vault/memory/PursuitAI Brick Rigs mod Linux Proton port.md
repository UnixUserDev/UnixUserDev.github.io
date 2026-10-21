---
tags:
  - memory/gaming
created: 2026-10-20T22:43:37
source: agent
---

Ported the Windows-only "PursuitAI Alpha 1.0.1" Brick Rigs mod (by MR.HOGE) to Linux in /OpenCode-Sandbox/pursuitai-linux/PursuitAI-Linux-1.0.1. Installer is bash: pursuit.sh install|status|disable|uninstall|verify, lib/{common,find-game,vehicles}.sh, backups+rollback, manifest.sha256.json.

Why a port was needed at all: Brick Rigs on this box is the native UE4.27 Linux build (Steam appid 552100), and UE4SS is a Windows DLL injector, so the mod only runs on the Windows depot under Proton (installed via Force compatibility; compatdata/552100/pfx). The vehicle pack + metadata are pure data and work either way. The 15 vehicles go to <prefix>/drive_c/users/steamuser/AppData/Local/BrickRigs/SavedRemastered/Vehicles, and UE4SS-settings.ini needs Windows Z: paths for ModsFolderPath because UE4SS runs inside the prefix while game files live on the host.

Audit result: package is not malware; both UE4SS zips are genuine upstream (v3.0.1 release is byte-identical to GitHub), 100/100 manifest hashes matched, no network/exec in any Lua, PNGs clean. The author's Windows installer is unusually well engineered (SHA256 pins, containment, rollback).

[[Inbox]]
