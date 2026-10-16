---
title: Preparing a CachyOS USB Installer
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-12-03
source: https://wiki.cachyos.org/installation/installation_prepare/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-12-03).

# Preparing a CachyOS USB Installer

## Before you start

- Installation is **online only** — a stable, reasonably fast internet connection is mandatory. Unstable internet means long install times or outright failure.
- **Not recommended in a VM** (broken/incorrect config is possible); bare metal is recommended.
- Minimum: **3 GB RAM**, **30 GB storage**, stable internet.
- USB drive: at least **8 GB** free.
- Keep a copy of the ISO on the drive (or elsewhere) for later troubleshooting via chroot.

## x86-64 microarchitecture levels

- **x86-64-v3** — AMD Family 15h (Excavator), 17h (Zen/Zen+/Zen 2), 19h (Zen 3); Intel 4th Gen (Haswell) through 10th Gen (Comet Lake), and 12th–15th Gen (Alder/Raptor/Raptor Refresh/Lunar/Arrow Lake).
- **x86-64-v4** — AMD 19h (Zen 4/Zen 4c), 1Ah (Zen 5/Zen 5c); Intel Skylake X, Cannon Lake, Ice/Cascade/Cooper Lake, Tiger/Sapphire/Emerald Rapids, Rocket Lake.

Relevant to [[Optimized Repositories and x86-64 Levels]].

## Writing the USB

### balenaEtcher (Linux/macOS/Windows)

```bash
# Extract zip file:
bsdtar xvf <zip file name>
# cd to the new folder:
cd balenaEtcher-linux-x64/
# Execute balenaEtcher:
./balena-etcher
```

### CLI (Linux & macOS)

Identify the drive first:

```bash
# Linux — the USB drive could be e.g. /dev/sda, by disk model and size
sudo fdisk -l

# macOS — USB drives are identified as /dev/diskN where N is 0,1,...
diskutil list
```

Then write it (replace `<usbdrive>` with the drive label):

```bash
sudo dd bs=4M if=full_iso_name.iso of=/dev/<usbdrive> status=progress oflag=sync
```

### Rufus (Windows only)

Device -> Boot selection -> SELECT ISO -> START.

### Ventoy (Linux & Windows)

```bash
# Replace version with the correct one, e.g. ventoy-1.1.07-linux.tar.gz
bsdtar xvf ventoy-version-linux.tar.gz
# cd to the new folder (version might change)
cd ventoy-1.1.07/
```

```bash
# It will ask for administrative privileges
./VentoyGUI.x86_64
```

Then place the ISO into the new usable partition of the drive.

Next: [[Installing CachyOS on a Desktop or Laptop]].
