---
title: CachyOS Filesystems
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-06-12
source: https://wiki.cachyos.org/installation/filesystem/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-06-12).

# CachyOS Filesystems

The installer offers **five**: XFS, BTRFS, EXT4, ZFS, F2FS — each with its utilities preinstalled.

**BTRFS is the default and recommended.** "Choose it if unsure."

## XFS

- Journaling filesystem by SGI (1993; ported to Linux 2001).
- Pro: built for speed and extreme scalability; multiple anti-corruption technologies; resistant to fragmentation (extent-based + delayed allocation).
- Con: **cannot be shrunk.**
- Package: `xfsprogs`.
- For: simple, fast, reliable, no advanced features.

## BTRFS

- Copy-on-write filesystem (created 2007, declared stable in the kernel 2013).
- Pro: transparent compression — **CachyOS ships ZSTD level 3 by default**; snapshots (COW, so they take very little space); subvolumes; can grow **or** shrink; fast development.
- Con: sometimes needs defrag/balance; worse on rotational drives due to fragmentation.
- Package: `btrfs-progs`.
- Default subvolume layout: `@` = `/`, `@home` = `/home`, `@root` = `/root`, `@srv` = `/srv`, `@cache` = `/var/cache`, `@tmp` = `/var/tmp`, `@log` = `/var/log`.
- For: snapshot/backup functionality plus transparent compression.

## EXT4

- Most commonly used Linux filesystem (stable in kernel 2008).
- Pro: can be as fast or faster than XFS in some cases; huge amount of documentation; proven; can grow or shrink (shrinking is offline only and requires the FS to be unmounted).
- Con: lacks many advanced features.
- Package: `e2fsprogs`.
- For: the simplest, most common choice.

## ZFS

- Advanced FS by Sun (2005). CDDL-licensed, so it **cannot live in the Linux kernel** and needs a separate module. **Do not use a real-time kernel with ZFS** (licensing incompatibility).
- Pro: pooled storage (`zpool`), COW snapshots, compression, Raid-Z, ARC cache (very fast reads of frequently accessed files).
- Con: complicated; ARC needs a lot of RAM to be effective; out-of-tree OpenZFS module; incompatible with real-time preemption.
- Tools: the `ZFS-Module` (CachyOS precompiles a zfs module per kernel version) and `zfs-utils`.
- For: advanced users who want pooled storage or ARC.

## F2FS

- Samsung's Flash-Friendly File System, built for NAND flash.
- Pro: flash-friendly design; transparent compression reduces writes (the space savings are not currently usable by the user); better wear leveling, longer NAND life.
- Con: cannot shrink; compression savings unusable; weak `fsck`; downgrading to a kernel older than the one that created the FS may break things; needs a workaround with GRUB on MBR/BIOS.
- Package: `f2fs-tools`.
- For: maximizing NAND lifespan. **Limine is the recommended bootloader for F2FS on MBR/BIOS** (no workaround needed, unlike GRUB).

## TL;DR

- Default **BTRFS** — stable, snapshots, compression.
- **XFS** or **EXT4** — simple and fast.

Related: [[Btrfs Snapshots]], [[CachyOS Boot Managers]].
