---
tags:
  - memory/gaming
created: 2026-10-23T17:05:41
source: agent
---

On the CachyOS box (RTX 4060, NVIDIA proprietary, Xwayland :0), native Linux Garry's Mod hangs forever on the loading screen with a full progress bar. Hosting a local server it aborts ~15s in (SIGABRT, ~90MB core, right after `Game.so loaded for "My Garry's Mod Server"`).

Diagnosis: gdb attached to the live process shows the main thread parked on one instruction inside `shaderapidx9.so` -> `materialsystem.so` -> `stdshader_dx9.so` -> `client.so` — the threaded material/shader queue dead-waiting during world load. Console shows `Could not load program cache file glbaseshaders.cfg`, `Could not find base GL shader cache file`, and every menu background `Failed to create material for background` -> `No main menu backgrounds found!` (grey main menu).

Fix that worked: `mat_queue_mode "0"` (single-threaded material queue). Written to `garrysmod/cfg/autoexec.cfg`; map then loads normally. Alternative if it recurs: run the game under Proton instead of the native build.

Side note: the game is on the USB HDD `/mnt/External/SteamLibrary`; host has no 32-bit `libXi.so.6` (`lib32-libxi` not installed), so launching hl2.sh outside Steam dies with `Couldn't load garrysmod/bin/client.so — libXi.so.6: cannot open shared object file` — Steam's runtime container masks that.

[[Inbox]]
