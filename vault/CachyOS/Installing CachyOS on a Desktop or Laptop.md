---
title: Installing CachyOS on a Desktop or Laptop
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-05-20
source: https://wiki.cachyos.org/installation/installation_on_root/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-05-20).

# Installing CachyOS on a Desktop or Laptop

## Before you begin

- If only **GRUB** and **Limine** show up as boot manager options after `Launch Installer`, the system booted in **Legacy/BIOS** mode.
- `Legacy USB Support` should be `Auto`.
- `Install alongside` and `Replace partition` "are not 100% reliable and can cause the installation to fail."
- **Manual partitioning is strongly preferred.**
- After a failed install, reboot into the ISO — the installer does not properly unmount partitions on failure, which causes continuous errors.
- Secure Boot is configured **post-install** (separate wiki page).

Check the boot mode:

```bash
efibootmgr -v
```

If it prints `EFI variables are not supported on this system.` you booted Legacy/BIOS — enable UEFI in firmware settings.

## Manual partitioning (UEFI/GPT)

Flow: Launch Installer -> Language + Region/Timezone -> Keyboard Layout -> **Manual partitioning** -> boot partition -> root partition -> DE -> packages -> login credentials -> summary -> **Install Now**. (MBR/BIOS has its own tab.)

**Boot partition (step 5)**

- **Limine**: ≥ **4096 MiB**, **FAT32**, mount `/boot`, flags `boot`.
- **systemd-boot & rEFInd**: same parameters as Limine; the partition table varies per boot manager.
- **GRUB**: follow the GRUB tab's parameters.

**Root partition (step 6)**

- Size ≥ **20000 MiB**, filesystem **any** (see [[CachyOS Filesystems]]), mount `/`, no flags.

## Dual booting with Windows

- Not 100% reliable: Windows updates can overwrite the EFI partition and break the bootloader. Separate drives avoid this.
- The EFI partition may need resizing to meet the boot manager's minimum size.
- FAT filesystems cannot be resized in place — back up the EFI filesystem, resize + reformat with something like `gparted`, then copy the filesystem structure back.

Windows-side prerequisites:

```powershell
powercfg /H off
```

(Run in PowerShell as administrator — disables Fast Startup and Hibernation; reboot after.)

```powershell
manage-bde -status
```

(Encryption Method showing `None` = BitLocker disabled; BitLocker must be **off**.)

Also required: Secure Boot disabled, a ≥30 GB empty partition (shrink a Windows partition via `Win + R` -> `diskmgmt.msc` -> right-click the main partition, usually `C:` -> `Shrink Volume...` -> at least **30720 MB**), and a bootable CachyOS USB.

Limine should auto-detect the Windows EFI partition. If Windows does not appear in the boot menu:

```bash
sudo limine-scan
```

Related: [[Preparing a CachyOS USB Installer]], [[CachyOS Boot Managers]], [[CachyOS Desktop Environments]].
