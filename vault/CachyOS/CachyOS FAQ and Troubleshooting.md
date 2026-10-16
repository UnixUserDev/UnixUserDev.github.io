---
title: CachyOS FAQ and Troubleshooting
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-10-03
source: https://wiki.cachyos.org/cachyos_basic/faq/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-10-03).

Applies to CachyOS and Arch-based distros; explicitly **not** a replacement for the Arch Wiki (`https://wiki.archlinux.org/title/Main_page`). The page itself warns against trusting AI-model-generated commands unverified.

# Reporting bugs

- GitHub `CachyOS/distribution`, forum `discuss.cachyos.org/c/feedback/bugreports/10`, Discord support channel, r/cachyos. Read the pinned support guidelines first.
- Simplified man pages:

```bash
tldr git
tldr git add
```

# Log gathering

The one-command bug report (collects `dmesg`, `journalctl`, `inxi`, then offers to upload to the CachyOS paste site):

```bash
sudo cachyos-bugreport.sh
```

Run a failing GUI program from a terminal, or capture its output:

```bash
firefox &> firefox-log.txt
```

Press `CTRL + C` after it fails to start so the output gets written.

Recently upgraded packages:

```bash
grep "\[ALPM\] upgraded" /var/log/pacman.log | tail -n 50
```

- Priorities: `debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`; numeric `0`=emerg, `1`=alert, `2`=crit, `3`=err, `4`=warning, `5`=notice, `6`=info, `7`=debug.
- `journalctl -k` reads kernel messages from the systemd journal (persistent across reboots if configured); `dmesg` reads the volatile kernel ring buffer (lost after reboot).

Useful `journalctl` invocations (from the wiki page):

```bash
journalctl
journalctl -b
journalctl -u sshd -u polkit -b -0 | grep -i "fail\|error\|denied"
journalctl -f
journalctl -u adguardhome -f
journalctl --user -u pipewire -u pipewire-pulse -u wireplumber -b 0
journalctl -u bluetooth -b 0
journalctl -u bluetooth -b -1
journalctl --since "10 minutes ago"
journalctl --since "1 hour ago"
journalctl --since "2024-01-15 14:30:00"
journalctl --since "09:00" --until "10:00"
journalctl -p err..emerg
journalctl _PID=3344
journalctl /usr/bin/firefox
```

Kernel messages:

```bash
journalctl -k
journalctl -k -b 0
journalctl -k -b -1
journalctl -k -f
journalctl -k | grep -i "nvidia\|amd\|intel"
journalctl -k | grep -i "usb\|pci"
journalctl -k | grep -i "memory\|ram"
journalctl -k --since "1 hour ago"
```

`dmesg` equivalents:

```bash
dmesg
dmesg -T
dmesg | less
dmesg -l err,crit,alert,emerg
```

# Installation and live environment

- The live ISO ships **KDE Plasma only**. It exists to install CachyOS or to run `cachy-chroot` for recovery — test other DEs in a VM.
- Installer slow to start after "Launch Installer" = it is running `calamares-online.sh`, which needs internet. The script: removes old keyring files → updates Arch + CachyOS keyring packages → initializes and populates the pacman keyring → enables network time sync → checks boot type (UEFI or BIOS/MBR) to prompt for the bootloader.
- Installer stuck at **33%** = struggling to download packages (slow/unstable connection).

# Bootloader recovery (e.g. entries gone after a BIOS update)

1. Boot the live ISO.
2. Chroot into the installed system:

   ```bash
   sudo cachy-chroot
   ```

   With BTRFS + the CachyOS preset, answer `y` at the prompt. For extra partitions type `/boot` for systemd-boot, Limine or rEFInd, or `/boot/efi` for GRUB. If `/boot` was deleted, recreate it per the install guide's UEFI/GPT scheme. See [[cachy-chroot Recovery Helper]].
3. Reinstall the bootloader (Limine example):

   ```bash
   sudo limine-install
   ```
4. Reinstall the CachyOS kernel:

   ```bash
   sudo pacman -Syu linux-cachyos linux-cachyos-headers
   ```
5. `exit` the chroot, then reboot.

# Pacman troubleshooting

| Error | Cause / fix |
|---|---|
| `signature is invalid` | Outdated mirror or broken keyring. Resync with `sudo pacman -Syu`, rate mirrors, or reset keyrings. |
| `404 Not Found` | Package not on the current mirror; local DB out of sync → `sudo pacman -Syu`, then retry. |
| `could not remove` | Unmanageable pacman cache file → CachyOS Hello → **Troubleshooting** → **Clear package cache**, or `sudo rm -r /var/cache/pacman/pkg/*` |
| `File is corrupted (invalid or corrupted package (PGP signature))` | Keyring problem. |
| `unable to lock database` | Another pacman holds the lock, or a crash left `db.lck`. |
| `failed retrieving file … Connection timed out` / `Couldn't resolve host name` / `526` | Bad/slow mirrors → `sudo cachyos-rate-mirrors` then `sudo pacman -Syu`. |
| `warning: local is newer than…` | Stale mirror or downgrade in repo → `sudo pacman -Syuu` allows downgrades. |
| `failed to commit transaction (conflicting files)` | Files already exist from another source; remove the conflicting files manually, then re-run. |
| `ERROR: module not found: 'nvidia', 'nvidia_modeset', …` | chwd always enables Early Module Loading, and NVIDIA modules may be missing for other installed kernels → `sudo pacman -S nvidia`. |

Keyring reset sequence:

```bash
sudo rm -rf /etc/pacman.d/gnupg/
sudo pacman-key --init
sudo pacman-key --populate

sudo pacman-key --recv-keys F3B607488DB35A47 --keyserver keyserver.ubuntu.com
sudo pacman-key --lsign-key F3B607488DB35A47

sudo rm -R /var/lib/pacman/sync
```

If that fails, the problem is mirrors, not the keyring:

```bash
sudo cachyos-rate-mirrors
sudo pacman -Scc
```

Remove a stale lock file:

```bash
sudo rm /var/lib/pacman/db.lck
```

Example conflicting-file fix:

```bash
sudo rm /usr/lib/environment.d/10-gsk.conf
```

# Other answers

- **Discord asks for an unavailable update** — Discord self-updates ahead of the repos; follow the Arch Wiki's Discord fix guide.
- **Slow boot** — `systemd-analyze blame` or `systemd-analyze critical-chain`. If `cachyos-rate-mirrors.service` is the culprit, stop it running at boot:

  ```bash
  systemctl mask cachyos-rate-mirrors
  systemctl mask cachyos-rate-mirrors.timer
  ```
- **Name origin** — after the "Cachy" scheduler, the original name of the `cacULE` CPU scheduler (`github.com/hamadmarri/cacule-cpu-scheduler`). The project started in 2020–2021 when ptr1337 was collaborating with Hamad on cacULE, and the x86-64-v3 optimized distro was named after that scheduler.
- **`-git` packages** — usually update once on Monday, with exceptions.
- **`-bin` packages** — precompiled binaries; they do **not** get the same performance optimizations as source-built CachyOS packages.
- **Disable the Plymouth boot animation** — add kernel parameters:

  ```
  plymouth.enable=0 disablehooks=plymouth
  ```

  Regenerate per bootloader: systemd-boot `sudo sdboot-manage gen`; GRUB `sudo grub-mkconfig -o /boot/grub/grub.cfg`; Limine `sudo limine-mkinitcpio`. See [[CachyOS Boot Managers]].
- **Request a package** — CachyOS ships many precompiled AUR packages; request more via GitHub `CachyOS/distribution/issues`, the forum's repository category, or Discord.

# Security and best practices

- **AUR safety**: understand the PKGBUILD (variables `source`, `pkgname`; functions `build()`, `package()`); verify `source` URLs point at official/trusted sites; review install steps and `.install` scripts; check the maintainer's AUR history; verify checksums (SHA256, BLAKE2b) and PGP signatures (`validpgpkeys`); be extra cautious with `-bin` packages; read the AUR comments; **never** use `--skipinteg` or similar bypass flags; control your AUR helper (yay, paru) so it shows the PKGBUILD and diffs, or build manually with `makepkg`; ask whether you need the package at all; keep the system updated with `sudo pacman -Syu`.
- **GUI package managers** — **Pamac** is known to improperly handle tasks such as corrupting system package keyrings, causing PGP signature errors that block updates. **Discover (KDE)** and **GNOME Software Center** use PackageKit: generally safe for Flatpaks, risky for system packages, and can crash mid-transaction. Recommended: `pacman` on the CLI; if you want a GUI, **Shelly** or the **CachyOS Package Installer** (direct pacman wrappers).
