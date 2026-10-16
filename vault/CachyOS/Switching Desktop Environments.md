---
title: Switching Desktop Environments
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-25
source: https://wiki.cachyos.org/configuration/desktop_environments/switch_desktop/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-25).

# Switching Desktop Environments

## Things to consider

- Multiple DEs/WMs can conflict — especially settings managers, default applications, and appearance configs such as GTK/Qt themes and icons. **The two worst offenders are GNOME and KDE Plasma** (different toolkits — GTK vs Qt — plus many background services).
- Package bloat matters for mainstream DEs (hundreds of packages).
- **Window managers mix with DEs more easily**, e.g. KDE Plasma + Hyprland, picked at login.

## How CachyOS settings packages work

Each CachyOS-offered DE/WM has its own `-settings` package, e.g. `cachyos-i3wm-settings`. These place config files in **`/etc/skel/`** — the template used for new user accounts.

> **Important:** for existing users these settings are **NOT applied automatically**, to avoid overwriting personal configs.

Two install choices:

1. Keep your current config files and manually adjust/copy from `/etc/skel` as needed (e.g. copy only i3wm-related files from `/etc/skel/.config/i3` into your home directory).
2. Overwrite your configs with the ones from `/etc/skel/` for the full CachyOS experience.

## Procedure

1. Back up your current configs:

   ```bash
   mkdir -p ~/config-backup
   cp -r ~/.config ~/config-backup/
   cp -r ~/.local ~/config-backup/
   ```
2. Uninstall the current `-settings` package, e.g. KDE:

   ```bash
   sudo pacman -R cachyos-kde-settings
   ```

   This does not remove your current DE/WM configurations — only the files stored in `/etc/skel`.
3. Install the new DE/WM plus its `-settings` package, e.g. i3wm:

   ```bash
   sudo pacman -Syu i3-wm cachyos-i3wm-settings
   ```

   Available DEs/WMs and packages: `https://github.com/CachyOS/CachyOS-PKGBUILDS`.
4. Copy the new config files from `/etc/skel` into your home directory. Example: `cachyos-i3wm-settings` carries config for Kvantum, dunst, GTK, i3, polybar, Qt5 and rofi.
   - File manager route: enable hidden files (`Ctrl+H`, or `View → Show Hidden Files`), go to `/etc/skel`, select all (`Ctrl+A`), copy (`Ctrl+C`), go to your home directory, paste (`Ctrl+V`), then choose "Merge"/"Apply to All" for folders and "Replace" for individual files you want to update — **careful, this overwrites current configs**.
   - Or do it in the terminal.
5. Log out; at the login screen pick the newly installed DE/WM from the session options before logging back in.

## FAQ

- **Reverting to your previous config** — restore from the backup:

  ```bash
  cp -r ~/config-backup/.config/* ~/.config/
  cp -r ~/config-backup/.local/* ~/.local/
  ```
- **New updates to a `-settings` package** — new files go to `/etc/skel/`, your user config in `/home/` stays untouched; apply updates by copying from `/etc/skel/` again. Watch the relevant GitHub repo.
- **Can I install multiple `-settings` packages?** Yes and no — multiple DEs/WMs can be installed, but **only one `-settings` package at a time** is recommended to avoid conflicts in `/etc/skel/`; otherwise pacman may complain about file conflicts.
- **What are "CachyOS Settings"?** Convenience configuration packages for various DEs/WMs providing pre-configured settings, themes and tweaks. Installing one also pulls in dependencies the vanilla DE/WM doesn't ship, such as themes, icons, wallpapers and extra apps — e.g. `cachyos-i3wm-settings` installs `rofi`, `dunst`, `picom`, `lxappearance` and more.

Maintained repos: KDE Plasma `CachyOS/cachyos-kde-settings`, GNOME `CachyOS/cachyos-gnome-settings`, Niri `cachyos/cachyos-niri-noctalia`, Hyprland `CachyOS/cachyos-hypr-noctalia`, MangoWM `CachyOS/cachyos-mangowc-dms`, i3 `CachyOS/cachyos-i3wm-settings`, Qtile `CachyOS/cachyos-qtile-settings`, Wayfire `CachyOS/cachyos-wayfire-settings`. All other offered DEs/WMs have no `-settings` package yet (vanilla versions).

Related: [[KDE Plasma on CachyOS]], [[Niri Keybinds and FAQ]], [[Hyprland Setup and Keybinds]], [[i3 Keybinds and FAQ]], [[Qtile Keybinds and FAQ]].
