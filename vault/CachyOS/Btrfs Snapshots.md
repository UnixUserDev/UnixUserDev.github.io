---
title: Btrfs Snapshots
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-06
source: https://wiki.cachyos.org/configuration/btrfs_snapshots/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-06).

> **Snapshots are not backups.** A snapshot is a read-only or read-write (default) copy of a subvolume at a point in time, space-efficient because it references the original data until something changes. It lives on the same disk, so it dies with the disk.

- CachyOS default subvolumes: `/`, `/root`, `/home`, `/var/cache`, `/var/tmp`, `/var/log`, `/srv`.
- Good for: recovery from breakage or corruption after updates, a misconfig that prevents boot, accidental deletion.
- **Not** useful for: bootloader problems, hardware failure.
- CachyOS ships **`snap-pac` by default**, so Snapper automatically creates snapshots before and after every pacman install, upgrade or removal.
- Guidance: keep roughly **10 snapshots** max, use them for recovery, and give each a meaningful description.

# GUI — Btrfs Assistant

Requires `btrfsmaintenance`.

```bash
btrfs-assistant-launcher
```

```bash
sudo -E btrfs-assistant
```

Retention settings (root config selected): Hourly/Daily/Weekly/Monthly/Yearly = `0`; untick **Enable timeline snapshots**; Number = `10`; tick **Snapper cleanup enabled**; Save + **Apply systemd changes** → enables `snapper-cleanup.timer`.

Verify in `/etc/snapper/configs/root`; expected:

```
# limit for number cleanup
NUMBER_MIN_AGE="3600"
NUMBER_LIMIT="10"
NUMBER_LIMIT_IMPORTANT="10"
```

Delete snapshots with Btrfs Assistant's `Delete` button, then regenerate bootloader entries (systemd-boot / GRUB / Limine — see [[Boot Manager Configuration]]).

# CLI — Snapper

Snapper comes from openSUSE and is the underlying tool. **CachyOS's default Snapper config only covers the root subvolume (`/`)** — other subvolumes need their own configs.

```bash
# Show the current configuration
sudo snapper get-config

# List snapshots and details
sudo snapper list

# Create a snapshot
sudo snapper create --type single --description "My Snapshot Description"

# Modify a snapshot
sudo snapper modify <option> <snapshot_number>
sudo snapper modify 729 --description 'My New Snapshot Description'

# Delete one snapshot
sudo snapper delete <snapshot_number>

# Delete a range
sudo snapper delete 718-721
```

`modify` options: `--description/-d`, `--cleanup-algorithm/-c`, `--userdata/-u`, `--read-only`, `--read-write`, `--default`.

`snapper list` columns: `#` (snapshot number, used by other commands), `Type` (`single` = standalone, `pre` = before a system change, `post` = after, linked to a `pre` snapshot), `Pre #` (the paired pre snapshot), `Date`, `User`, `Cleanup` (algorithm: `number`, `timeline`, or `empty-pre-post`), `Description`, `Userdata`.

> **Snapshot 0 cannot be deleted** — it represents the current state of the filesystem.

Limine snapshot menu limit — back up first, then edit:

```bash
sudo cp /etc/limine-snapper-sync.conf /etc/limine-snapper-sync.conf.orig
sudo nano /etc/limine-snapper-sync.conf
```

Set `MAX_SNAPSHOT_ENTRIES=10`; `SNAPSHOT_FORMAT_CHOICE=8` is recommended (date, time, description).

> **Gotcha:** if a snapshot involved a kernel update, you cannot roll back to it.

GRUB users need the scripts/hooks that add snapshots to the menu:

```bash
sudo pacman -Syu grub-btrfs-support
```

Docs: `snapper.io/manpages/snapper.html`, `snapper.io/manpages/snapper-configs.html`, Arch Wiki `Snapper`.

Related: [[CachyOS Filesystems]], [[CachyOS Boot Managers]], [[Boot Manager Configuration]].
