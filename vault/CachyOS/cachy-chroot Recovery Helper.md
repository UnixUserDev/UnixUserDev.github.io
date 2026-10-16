---
title: cachy-chroot Recovery Helper
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-25
source: https://wiki.cachyos.org/features/cachy_chroot/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-25).

# cachy-chroot Recovery Helper

`cachy-chroot` (`https://github.com/CachyOS/cachy-chroot`) chroots into an existing CachyOS or Arch-based install.

- Lists every partition found on the machine, supports listing BTRFS subvolumes, and supports encrypted systems via LUKS.
- Maps each `fstab` entry to its `crypttab` entry and **gracefully closes all LUKS volumes on exit**.
- Supports essentially any filesystem the Linux kernel supports — **except ZFS**.

## Usage

Navigate with arrow keys, select with `ENTER`. `❯` marks the current selection; `?` means it's prompting for input.

1. Boot a CachyOS live ISO.
2. Open a terminal and become root: `sudo su`.
3. Make sure you have the latest version:

   ```bash
   pacman -Sy cachy-chroot
   ```
4. Run it:

   ```bash
   cachy-chroot
   ```

   It scans and lists all available partitions.
5. Select the partition holding the root filesystem.
6. It tries to auto-mount every partition and subvolume listed in the root device's `/etc/fstab`. Failed mounts are reported and can be mounted manually; answer `no` to skip.
7. You are in the chroot; the prompt looks like:

   ```
   [root@CachyOS /]#
   ```

   Example maintenance commands:

   ```bash
   pacman -Syu
   ```

   ```bash
   mkinitcpio -P
   ```
8. Exit with `exit` or `CTRL+D`.
9. On exit it cleans up mounted partitions and closes LUKS containers, returning you to the live shell.

## Troubleshooting

- **No partitions found** — make sure the disk is connected and recognized by the live system; check `lsblk` or `fdisk -l`.
- **Automount fails for some partitions** — mount them manually from inside the chroot; `cachy-chroot` ignores failures and continues.

## FAQ

- **Mounting extra partitions** — useful for a broken/missing `fstab` (mount `/boot` or `/home` manually to repair or recover data), or after partition UUIDs changed (mount and fix `fstab` when the system won't boot).
- Arch Wiki chroot reference: `https://wiki.archlinux.org/title/Chroot`.

Related: [[CachyOS FAQ and Troubleshooting]] (bootloader recovery walks through this).
