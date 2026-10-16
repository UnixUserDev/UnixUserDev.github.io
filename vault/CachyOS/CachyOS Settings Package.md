---
title: CachyOS Settings Package
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-30
source: https://wiki.cachyos.org/features/cachyos_settings/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-30).

# CachyOS Settings Package

Everything below ships in the `cachyos-settings` package. Upstream: `https://github.com/CachyOS/CachyOS-Settings`.

## Helper scripts

- **`cachyos-bugreport.sh`** — writes a log file to your home directory with system info + logs, then offers to upload to the CachyOS pastebin.

  ```bash
  sudo cachyos-bugreport.sh
  ```
- **DLSS Swapper** — updates DLSS DLLs and presets. `dlss-swapper` pulls the newest DLLs/presets from the NGX Updater; `dlss-swapper-dll` uses DLLs already in the game directory. Proton-CachyOS has built-in upgrade functionality, and **`PROTON_DLSS_UPGRADE=1` must not be combined with `dlss-swapper`** — they do the same thing and conflict.
- **`game-performance`** — enables the `powerprofilesctl` performance profile for the target process (games or anything else) and disables the screensaver while it runs.
- **`kerver`** — reports current kernel version (checks x86_64 support and CPU compiler optimizations), current I/O scheduler, and a summary of CPU / I/O / sched-ext schedulers if active.
- **`paste-cachyos`** — CLI uploader to `https://paste.cachyos.org`; content must be piped in:

  ```bash
  <command> | paste-cachyos
  ```
- **`pci-latency`** — tries to reduce latency / improve performance for PCI sound cards by changing the PCI latency timer. Ships as a binary (on demand) and a systemd service (applies at boot).
- **`sbctl-batch-sign`** — helper for signing the files Secure Boot needs; useful when dual booting Windows (lots of EFI files). Scans for unsigned files with `sbctl verify`, signs with `sbctl sign` when needed, and skips files belonging to Windows, GRUB, and the `.mui`/`.dll` extensions. **Not compatible with Limine.**
- **`topmem`** — Lua script showing the top memory-consuming processes by RAM, SWAP and KSM (Kernel Same-page Merging) sharing.
- **`zink-run`** — forces Zink (OpenGL over Vulkan) for the target process via environment variables; useful in Steam for games that behave better under Zink.

## Overrides

An override is a config file you create to change `cachyos-settings` defaults without touching the originals.

- **sysctl** — tweaks are documented in `/usr/lib/sysctl.d/70-cachyos-settings.conf`; copy the entry you want into a new modified file under `/etc/sysctl.d/`.
- **udev** — same idea: create your own file under `/etc/udev/rules.d/` with the same name as the original. List the shipped udev files with:

  ```bash
  pacman -Ql cachyos-settings | grep udev
  ```

Related: [[CachyOS Kernels]], [[General System Tweaks]].
