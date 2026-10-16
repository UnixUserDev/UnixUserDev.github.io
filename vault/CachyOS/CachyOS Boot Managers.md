---
title: CachyOS Boot Managers
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-12-21
source: https://wiki.cachyos.org/installation/boot_managers/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-12-21).

# CachyOS Boot Managers

Available: **systemd-boot, rEFInd, GRUB, Limine**.

> Warning: some **MSI motherboards** have UEFI firmware that is not fully spec-compliant, which causes compatibility issues with some boot managers (including GRUB and rEFInd). If affected, use **systemd-boot** or **Limine**.

## Comparison highlights

- **Firmware support** — systemd-boot: UEFI only. rEFInd: UEFI only. GRUB: UEFI **and** BIOS. Limine: UEFI **and** BIOS.
- **`/boot` filesystem** — systemd-boot: whatever the firmware supports (usually FAT12/16/32), more with EFI drivers. rEFInd: firmware support plus ext2/3/4, Btrfs, ISO-9660, HFS+, NTFS. GRUB: broad (ext*, Btrfs, XFS, …). Limine: FAT12/16/32 and ISO9660.
- **Windows dual boot** — systemd-boot auto-detects Windows Boot Manager on the same ESP; rEFInd auto-detects EFI loaders and kernels; GRUB via `os-prober` or manual config; Limine via `limine-scan`.
- **Btrfs snapshot integration** — systemd-boot/rEFInd only via custom setups (not provided by CachyOS); GRUB via `grub-btrfs-support`; Limine via `limine-snapper-sync` (enabled by default on CachyOS with Btrfs).
- **Full disk encryption** — systemd-boot/rEFInd/Limine: possible, root encrypted, `/boot` must stay unencrypted. GRUB: fully supported including encrypted `/boot` via `cryptodisk` (**LUKS1/PBKDF2 only**; LUKS2 is limited/needs workarounds).
- **Theming** — systemd-boot: minimal, none. rEFInd: graphical, themeable. GRUB: classic, themeable. Limine: themeable menu with skins.
- **TPM PCR measured boot** — built in for systemd-boot and GRUB; rEFInd and Limine can via `systemd-ukify`.

## systemd-boot

- Pro: very simple config, entries in separate files, works on some problematic MSI boards, config auto-generated on CachyOS, TPM PCRs measured during boot.
- Con: no BIOS/MBR; barebones, no theming; non-firmware boot FS needs separate ESP + XBOOTLDR partitions and EFI drivers; cannot find boot images outside its own ESP/XBOOTLDR; no native Btrfs snapshot rollback (kernel images live on the boot partition) — snapshot booting only through custom setups CachyOS does not provide.

## rEFInd

- Fork of rEFIt.
- Pro: auto-detects all OSes and kernels, little to no manual config, graphical macOS-style boot selector, great theming, optional touchscreen, reads images from EFI filesystems plus EXT4 and BTRFS (more via EFI drivers from the `efifs` package).
- Con: no BIOS support; incompatible with some MSI boards; TPM PCRs not measured (fails the PCR0 reconstruction test) — fixable via UKI with systemd-stub (`systemd-ukify`) or by chainloading a bootloader that measures PCR.

## GRUB

- Oldest, largest feature set, most widely used.
- Pro: supports nearly all Linux filesystems; abundant docs and community; supports encrypted `/boot`; Btrfs snapshot booting via `grub-btrfs-support`; supports BIOS and UEFI; themeable; TPM PCRs measured.
- Con: large and complex with many FS drivers; noticeably slower than systemd-boot/rEFInd/Limine; incompatible with some MSI boards.

## Limine

- Modern, portable multiprotocol bootloader; reference implementation for the Limine boot protocol; supports Linux and chainloading.
- Pro: multiple boot protocols (Multiboot2, Linux boot protocol); boots on UEFI and BIOS; GRUB-like theming; Btrfs snapshots through `limine-snapper-sync`, enabled by default on CachyOS with Btrfs.
- Con: `/boot` must be FAT12/16/32 or ISO9660; does not auto-add a UEFI NVRAM entry — use `efibootmgr` manually or `limine-entry-tool` (preinstalled); no UFS; TPM PCRs not measured (same UKI/chainload workarounds as rEFInd).

## TL;DR

- **GRUB** — encrypted `/boot`, BIOS, mature Btrfs snapshots.
- **Limine** — modern, out-of-box Btrfs snapshots, BIOS/UEFI, Windows dual boot via `limine-scan`.
- **rEFInd** — polished graphical UI plus automatic multi-boot on UEFI.
- **systemd-boot** — simplest setup, no snapshots needed; most reliable fallback for MSI UEFI quirks.

Related: [[Boot Manager Configuration]], [[CachyOS Filesystems]], [[Btrfs Snapshots]].
