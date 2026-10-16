---
title: Boot Manager Configuration
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-05-22
source: https://wiki.cachyos.org/configuration/boot_manager_configuration/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-05-22). Where each bootloader's settings actually live. See also [[CachyOS Boot Managers]] for choosing one.

# systemd-boot

Two kinds of config file: systemd-boot itself in `/boot/loader/loader.conf`, and one file per kernel entry in `/boot/loader/entry`. Loader config sets the default entry and timeout:

```
/boot/loader/loader.conf
default @saved
timeout 5
#console-mode keep # This option configures the resolution of the console.
```

Kernel command line: CachyOS provides `sdboot-manage`, configured in `/etc/sdboot-manage.conf`. Edit `LINUX_OPTIONS=`:

```
LINUX_OPTIONS="zswap.enabled=0 nowatchdog quiet splash"
```

Then regenerate every entry:

```bash
sudo sdboot-manage gen
```

# rEFInd

Two config files: `refind.conf` in `boot/efi/EFI/refind` (behaviour; extensively commented) and `/boot/refind_linux.conf` (boot options). Kernel parameters go in "Boot using default options":

```
/boot/refind_linux.conf
"Boot using default options"     "root=PARTUUID=1cb353ec-7f03-4820-8b4b-03baf53a208f rw zswap.enabled=0 nowatchdog quiet splash"
```

Changes to both files take effect immediately — nothing to regenerate.

# GRUB

One config file: `/etc/default/grub`.

- Hide the boot menu:

  ```
  /etc/default/grub
  GRUB_TIMEOUT='0'
  GRUB_TIMEOUT_STYLE=hidden
  ```

  Press ESC at boot for the GRUB prompt; run `normal` or `exit` to get back to the menu.
- Kernel command line — edit `GRUB_CMDLINE_LINUX_DEFAULT`:

  ```
  /etc/default/grub
  GRUB_CMDLINE_LINUX_DEFAULT='nowatchdog zswap.enabled=0 quiet splash'
  ```
- **Every change requires**:

  ```bash
  sudo grub-mkconfig -o /boot/grub/grub.cfg
  ```
- Bootable BTRFS snapshots: enabled by default since the August release. For older installs:

  ```bash
  sudo pacman -S grub-btrfs-support
  ```

  which installs scripts and hooks so snapshots appear in the GRUB menu. See [[Btrfs Snapshots]].

# Limine

Menu settings live in `/boot/limine.conf` (sometimes on the EFI system partition); kernel parameters in `/etc/default/limine`. `limine.conf` changes take effect as soon as you save.

- Menu: `timeout: 5` sets the autoboot wait; `default_entry: 2 # Boot the second entry by default` picks the default (entries are numbered from 1; unset = 1). **If `default_entry` points at a directory** (e.g. `/+CachyOS`), autoboot is disabled — it must point at a specific entry number.
- Entry example uses `protocol: linux`, `kernel_path: boot():/vmlinuz-linux-cachyos`, `cmdline: quiet splash root=UUID=... rw`, `module_path: boot():/initramfs-linux-cachyos.img`; `boot():/` is the root of the boot drive.
- Theming: `wallpaper:` (BMP, PNG, JPEG) plus `wallpaper_style:` (`stretched`, `tiled`, `centered`) and `backdrop:` (RRGGBB) when centered; `term_font:` (custom font file) and `term_font_scale: 2x2`; `term_background:` (AARRGGBB).
- Kernel entries are **automatically managed**: `limine-mkinitcpio-hook` uses `limine-entry-tool` in the background as kernels are installed/removed. Parameters are set with `KERNEL_CMDLINE` variables in `/etc/default/limine`, per default / per kernel name / for fallbacks:

  ```
  /etc/default/limine
  # Default parameters for most kernels
  KERNEL_CMDLINE[default]="quiet splash rd.udev.log_priority=3"

  # Specific parameters for the 'linux-cachyos' kernel
  KERNEL_CMDLINE["linux-cachyos"]="quiet splash mitigations=off"

  # Parameters for fallback entries (if generated)
  # KERNEL_CMDLINE[fallback]="..."
  ```

  Apply with:

  ```bash
  sudo limine-mkinitcpio
  ```

  which triggers mkinitcpio including `limine-mkinitcpio-hook`, landing the changes in `/boot/limine.conf`.

Related: [[Secure Boot Setup]], [[Btrfs Snapshots]], [[CachyOS FAQ and Troubleshooting]].
