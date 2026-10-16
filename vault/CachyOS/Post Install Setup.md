---
title: Post Install Setup
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-27
source: https://wiki.cachyos.org/configuration/post_install_setup/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-27). Recommended steps, in order.

# 1. Update the system

Options: Shelly (GUI & CLI), Octopi (GUI), Pacman, Offline System Update, Cachy-Update.

- Shelly CLI: `shelly`, `shelly install --upgrade {pkg}`, `shelly remove {pkg}`, `shelly aur search {pkg}`.
- Pacman:

  ```bash
  sudo pacman -Syu
  ```
- **Offline updates** via `pacman-offline`:

  ```bash
  sudo pacman -S pacman-offline
  ```

  Add `Include = /etc/pacman.d/offline.conf` to `/etc/pacman.conf` after the `[options]` section, then fill `/etc/pacman.d/offline.conf` with `IgnorePkg =` lines for the Arch and CachyOS kernels. Run once with `sudo systemctl start pacman-offline-prepare.service`; automate with `sudo systemctl enable pacman-offline-prepare.timer` and optionally `pacman-offline-reboot.timer` (reboots by default at 3am with a 2h randomized delay).
- **Cachy-Update** (fork of Arch-Update) — enable/disable in `CachyOS Hello > Apps/Tweaks`. Checks once two minutes after boot, then once a day with a one hour random delay. Timer override: `systemctl --user edit --full arch-update.timer`; defaults `OnStartupSec=2min`, `RandomizedDelaySec=1h`, `OnUnitActiveSec=1d`. Hide the tray:
  ```bash
  mkdir -p ~/.config/autostart
  echo -e "[Desktop Entry]\nHidden=true" > ~/.config/autostart/arch-update-tray.desktop
  ```
  Stop checks: `systemctl --user disable --now arch-update.timer`. Supports sudo, sudo-rs, doas, run0.
- **Reboot after a big update**, especially a kernel update.

# 2. Firewall (ufw)

> **Caution:** improperly configured firewall rules can lock you out of your own system. Be careful here.

Enabled by default after installation.

```bash
sudo ufw enable
sudo ufw disable
sudo ufw allow ssh
sudo ufw allow 12345
sudo ufw deny ssh
sudo ufw deny 12345
sudo ufw status verbose
```

Default policy: ignore incoming, allow outgoing. **Allow/deny changes take effect after ufw is restarted** (disable then enable for an immediate effect). Rules can carry comments: `sudo ufw allow 12345 comment 'Example'`. Also configurable graphically in KDE Plasma's Firewall settings.

# 3. Global menu

Some apps (VS Code, for instance) otherwise attach the menu to the parent app.

```bash
# To enable global menu support, run the command and restart the app.
sudo pacman -S appmenu-gtk-module libdbusmenu-glib
```

# Optional configurations

- **Default shell** — CachyOS uses **fish** as the user's login shell; bash remains root's. `chsh -s /usr/bin/bash` or `chsh -s /usr/bin/zsh` (CachyOS ships a zsh config aiming for 1:1 parity with its fish config).
- **tldr** — CachyOS uses **tealdeer**, a faster Rust implementation: `tldr --update`, then e.g. `tldr java`.
- **AppImages** — `chmod +x /path/to/your/appimage`; FUSE is required (`sudo pacman -S fuse2`). Manage them with Gear Lever: `sudo pacman -S flatpak`, restart, `flatpak install flathub it.mijorus.gearlever`, then log out/in.
- **Samba shares** — assumes a working Samba server. Back up `/etc/samba/smb.conf`, install `sudo pacman -S cachyos-samba-settings` on the client, copy its `smb.conf` to the server, edit shares, then `sudo systemctl restart --now samba`. Access via `smb://<your_server_ip>/<share_name>`.
- **AppArmor (apparmor.d profiles)** — the wiki is explicit: **do not proceed without understanding the implications.** Add the kernel parameter `lsm=landlock,lockdown,yama,integrity,apparmor,bpf` in the boot manager; `sudo pacman -S apparmor apparmor.d`; `systemctl enable --now apparmor.service`; add `write-cache`, `Optimize=compress-fast`, `cache-loc /etc/apparmor/earlypolicy/` to `/etc/apparmor/parser.conf`; save and reboot.
- **TRIM on encrypted (LUKS) devices** — CachyOS enables `fstrim.timer` by default, but **dm-crypt/LUKS blocks discard unless passthrough is explicitly enabled**, so periodic TRIM silently does nothing on encrypted partitions. Security caveat: someone with physical access could infer approximate used space, filesystem layout, or data patterns. Find the mapper with `lsblk` (look for the `crypt` type entry, e.g. `luks-1e647929-...`), then:

  ```bash
  sudo cryptsetup --allow-discards --persistent refresh <mapper_name>
  ```

  Reboot and verify:

  ```bash
  lsblk -D
  sudo fstrim -v /
  ```

  `lsblk -D` should show non-zero `DISC-GRAN` and `DISC-MAX`, and `fstrim` prints the amount trimmed.

Related: [[Btrfs Snapshots]], [[Boot Manager Configuration]], [[CachyOS Settings Package]].
