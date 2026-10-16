---
title: Optimized Repositories and x86-64 Levels
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-06-14
source: https://wiki.cachyos.org/features/optimized_repos/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-06-14).

# Optimized Repositories and x86-64 Levels

CachyOS recompiles Arch packages for `x86-64-v3`, `x86-64-v4`, and `Zen4+`.

- **x86-64-v3**: 5–20% performance uplift over baseline x86-64.
- **x86-64-v4**: substantial gains via AVX512, workload-dependent.
- **Zen 4/5**: v4 plus extra instructions:

  ```
  abm, adx, aes, avx512bf16, avx512bitalg, avx512ifma, avx512vbmi, avx512vbmi2, avx512vnni,avx512vpopctndq, clflushopt, clwb, clzero, fsgsbase, gfni, mwaitx, pclmul, pku, prfchw,rpdid, rdrnd, rdseed, sha, sse4a, vaes, vockmulqdq, wbnoinvd, savec, xsaveopt, xsaves
  ```

## Customized packages

The `CachyOS-PKGBUILDs` repo carries packages with ongoing updates, patches and backported fixes. Selective PGO, LTO and BOLT optimizations; some `-git` packages are maintained (e.g. `mesa-git`). Search at `https://packages.cachyos.org/` (shows compilation architecture, last update date, etc.).

## Migrating x86-64-v3 → x86-64-v4 or znver4

> **Trap:** Intel 12th gen (Alder Lake) and newer may *report* `x86-64-v4` but **cannot** run AVX-512 — P-cores support it, E-cores do not, which causes instability. Intel disabled it via microcode/BIOS updates (early chips; re-enable only by avoiding/rolling back microcode) and by hardware fusing (2022+ chips, permanent). **Treat these CPUs as x86-64-v3 only.**

Verify v4 support:

```bash
/lib/ld-linux-x86-64.so.2 --help | grep supported
```

A `x86-64-v4 (supported, searched)` line means supported; no v4 line means unsupported. Example:

```
$ /lib/ld-linux-x86-64.so.2 --help | grep supported
  x86-64-v2 (supported, searched)
  x86-64-v3 (supported, searched)
  x86-64-v4 (supported, searched)
```

For AMD Zen 4/5, check the reported arch:

```bash
gcc -march=native -Q --help=target 2>&1 | grep -Po "^\s+-march=\s+\K(\w+)$"
```

Output `znver4` or `znver5` means you can migrate. Both x86-64-v4 and znver4 use the same mirrorlist: `/etc/pacman.d/cachyos-v4-mirrorlist`.

Migration steps:

1. Edit `/etc/pacman.conf`, replacing the `x86-64-v3` repos. Keep `[cachyos]`, `[core]`, `[extra]`, `[multilib]` unchanged:

   ```ini
   [cachyos-v4]
   Include = /etc/pacman.d/cachyos-v4-mirrorlist

   [cachyos-core-v4]
   Include = /etc/pacman.d/cachyos-v4-mirrorlist

   [cachyos-extra-v4]
   Include = /etc/pacman.d/cachyos-v4-mirrorlist
   ```
2. Clear cache and sync DBs:

   ```bash
   sudo pacman -Scc   # Confirm with 'y' twice
   sudo pacman -Sy
   ```
3. Reinstall everything to switch architecture:

   ```bash
   pacman -Qqn | sudo pacman -S -
   ```
4. Reboot.

## Adding these repos to an existing Arch install

Check the CPU compatibility list in [[Preparing a CachyOS USB Installer]] first. Then the automated script (detects CPU instruction sets, installs the most optimized repos, backs up the old `pacman.conf`):

```bash
curl https://mirror.cachyos.org/cachyos-repo.tar.xz -o cachyos-repo.tar.xz
tar xvf cachyos-repo.tar.xz && cd cachyos-repo
sudo ./cachyos-repo.sh
```

## Uninstalling the repos

```bash
curl https://mirror.cachyos.org/cachyos-repo.tar.xz -o cachyos-repo.tar.xz
tar xvf cachyos-repo.tar.xz
cd cachyos-repo
sudo ./cachyos-repo.sh --remove
```

## Benchmarks

Phoronix (Michael Larabel) has benchmarked CachyOS repeatedly since 2022 — including an Arch x86_64-v2 RFC (2021), "The Performance Of Arch Linux Powered CachyOS" (2022-09-12), "Arch Linux CachyOS Benchmarks Of x86-64-v3 & x86-64-v4 Repositories" (2024-02-29), and a Ryzen 9 9950X OS comparison (2024-08-20). Caveat noted on the page: Liquid-DSP and RocksDB were compiled by the Phoronix suite ignoring `/etc/makepkg.conf` flags, giving unexpected CachyOS results.
