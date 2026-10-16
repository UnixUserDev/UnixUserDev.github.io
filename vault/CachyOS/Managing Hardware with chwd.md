---
title: Managing Hardware with chwd
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-03-22
source: https://wiki.cachyos.org/features/chwd/chwd/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-03-22).

# Managing Hardware with chwd

**chwd** = CachyOS Hardware Detection (`https://github.com/CachyOS/chwd/`). It installs the packages and drivers the running system needs: NVIDIA GPUs, T2 MacBooks, handhelds (Steam Deck, ROG Ally). It normally runs at install time but works post-install too.

Auto-configure (installs and configures drivers/packages so the system runs optimally):

```bash
sudo chwd -a
```

Install a specific profile — list them first:

```bash
chwd --list-all
```

Example PCI profiles and priorities from the wiki output: `nouveau` 18, `nvidia-dkms-470xx` 14, `nvidia-dkms-580xx` 12, `nvidia-open-dkms` 10, `macbook-t2` 9, `virtualmachine` 5, `amd` 4, `intel` 4, `fallback` 3, `handheld.intel-msi-claw` 2, `handheld.rog-ally` 2, `handheld.steam-deck` 2, `handheld` 1, `broadcom-wl` 1.

Install a profile:

```bash
sudo chwd -i amd
```

Output: `> Installing amd ...` then `> Successfully installed amd`.

Full option list, verbatim from `chwd --help`:

```
Usage: chwd [OPTIONS]
Options:
  -i, --install <profile>          Install profile
  -r, --remove <profile>           Remove profile
  -d, --detail                     Show detailed info for listings
  -f, --force                      Force reinstall
      --list-installed             List installed kernels
      --list                       List available profiles for all devices
      --list-all                   List all profiles
  -a, --autoconfigure [<classid>]  Autoconfigure
      --ai_sdk                     Toggle AI SDK profiles
      --pmcachedir <PMCACHEDIR>    [default: /var/cache/pacman/pkg]
      --pmconfig <PMCONFIG>        [default: /etc/pacman.conf]
      --pmroot <PMROOT>            [default: /]
  -h, --help                       Print help
  -V, --version                    Print version
```

The wiki defers to `chwd --help` for anything else.

Related: [[Switching Between NVIDIA and AMD GPUs]], [[Dual GPU Setup]].
