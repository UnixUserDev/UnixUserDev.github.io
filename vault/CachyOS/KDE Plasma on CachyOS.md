---
title: KDE Plasma on CachyOS
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-24
source: https://wiki.cachyos.org/configuration/desktop_environments/kde/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-24).

# KDE Plasma on CachyOS

Short page — an X11 session note, an `xorg-server` note, and two FAQ items.

## Missing X11 session

KDE split its development into multiple packages and the X11 session was removed from the default installation. Install it with:

```bash
sudo pacman -S plasma-x11-session kwin-x11
```

After installation, X11 appears as an option on the login screen.

## Missing `xorg-server` (black screen at login)

Since the CachyOS **26.01** installer, `xorg-server` is no longer installed — and neither `plasma-x11-session` nor `kwin-x11` pulls it in as a dependency. Not installing the Xorg server and its components manually results in a **black screen during login**.

```bash
sudo pacman -S xorg-server
```

## FAQ

**The "Emerald" look from the KDE screenshots** — it is the Emerald Theme plus the Qogir Icon Theme, neither installed by default:

```bash
sudo pacman -S cachyos-emerald-kde-theme-git qogir-icon-theme
```

Apply both in `Appearance & Style - Colors & Themes`.

**The login screen does not mirror the desktop** — the page only points at a screenshot (`kde-login-screen-settings.png`); no textual steps are given.

Related: [[Switching Desktop Environments]], [[CachyOS Desktop Environments]].
