---
title: Automounting Drives at Boot
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-27
source: https://wiki.cachyos.org/configuration/automount_with_fstab/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-27).

# Automounting Drives at Boot

Prerequisite: root/sudo access.

> NTFS warning: NTFS is unstable under Linux with the `ntfs`, `ntfs3` or `ntfs-3g` drivers. Prefer ext4/XFS/Btrfs.

Two methods:

- **systemd mount files** — safer; a mistake won't stop the machine booting.
- **`/etc/fstab`** — advanced; a mistake can make the system unbootable.

Find UUIDs:

```bash
lsblk -f
```

```bash
sudo fdisk -l
```

## Method 1 — systemd mount unit

Mount point `/home/user/games` → unit file `/etc/systemd/system/home-$USER-games.mount` (slashes become hyphens, dashes become escape codes, in `/etc/systemd/system`). For odd names:

```bash
systemd-escape -p --suffix=mount "/home/grzegorz/brzęczyszczykiewicz"
```

Template:

```
[Unit]
Description=
[Mount]
What=
Where=
Type=
Options=
[Install]
WantedBy=multi-user.target
```

Filled example:

```
[Unit]
Description=Mount games drive (/home/user/games)
[Mount]
What=UUID=4e55896f-3f1f-4bc5-aa8a-fec099689cd9
Where=/home/user/games
Type=ext4
Options=defaults,exec,rw,noatime
[Install]
WantedBy=multi-user.target
```

Enable it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now home-$USER-games.mount
```

Minimal version (`micro /etc/systemd/system/mnt-foo.mount`):

```
[Unit]
Description=Mount drive
[Mount]
What=UUID=<partition UUID>
Where=/mnt/foo
Type=somefs
Options=defaults
[Install]
WantedBy=multi-user.target
```

Options worth knowing: `_netdev` for NAS mounts, `nofail` for non-critical drives.

## Method 2 — /etc/fstab

```bash
sudo nano /etc/fstab
```

(The mount point `/media/windows` must exist.)

```
UUID=08A24E90A24E81E4 /media/windows ntfs3 defaults,nofail,uid=1000,gid=1000,rw,user,exec,umask=000 0 0
```

- `defaults` = `rw,suid,dev,exec,auto,nouser,async`
- `nofail` = keep booting if the mount fails
- `uid=1000` / `gid=1000` = ownership; `rw`; `user` = allow non-root mounting; `exec`; `umask=000` = permissive
- First `0` = dump (deprecated), second `0` = fsck order (root = 1, btrfs root = 0, others 0 or 2)
- Short form `UUID=<partition UUID> /media/foo somefs` is equivalent to `UUID=<partition UUID> /media/foo somefs defaults 0 0`

Apply:

```bash
sudo systemctl daemon-reload
```

```bash
sudo mount -a
```

Optional convenience symlink:

```bash
ln -s /media/windows ~/Windows
```
