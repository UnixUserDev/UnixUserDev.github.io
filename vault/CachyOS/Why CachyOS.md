---
title: Why CachyOS
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-01-27
source: https://wiki.cachyos.org/cachyos_basic/why_cachyos/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-01-27).

# Why CachyOS

A performance-centric Arch Linux distribution: rolling release with a custom toolchain.

## Optimized packages and repositories

- A large selection of packages recompiled for modern CPU architectures: `x86-64-v3`, `x86-64-v4`, and Zen4+ — see [[Optimized Repositories and x86-64 Levels]].
- Package browser: `https://packages.cachyos.org/`.

## Custom kernel

- The CachyOS base kernel patch set tunes kernel parameters to improve desktop responsiveness.
- Cherry-picks patch sets that are not mainlined or not in the kernel stable revision; these get internal testing before release so stability is not impacted. See [[CachyOS Kernels]].

## CPU scheduler support

- **EEVDF** (the default Linux scheduler): the CachyOS kernel carries custom EEVDF tunables to improve desktop responsiveness (throughput-oriented by default).
- **BORE** (Burst-Oriented Response Enhancer, `github.com/firelzrd/bore-scheduler`): enhances EEVDF for a more fluid experience under heavy workloads — for maximum interactivity. Ships in the `linux-cachyos-bore` variant. See [[sched-ext Schedulers]].

## User-friendly tools

- Custom hardware detection automatically identifies and installs the necessary drivers and packages, removing manual driver hunting after install.
- The installer lets you choose desktop environment, packages, filesystem, boot manager, kernel and more. Custom-package list source: `github.com/CachyOS/cachyos-calamares/blob/cachyos-limine-qt6/src/modules/netinstall/netinstall.yaml`.

## CachyOS-maintained applications

- **CachyOS Hello** (`github.com/CachyOS/CachyOS-Welcome`) — welcome app for tweaks, fixes, and package installs.
- **CachyOS Package Installer** (`github.com/CachyOS/packageinstaller`) — GUI for installing applications.
- **CachyOS Kernel Manager** (`github.com/CachyOS/kernel-manager`) — install repo kernels, configure your own, manage the sched-ext framework.
- **cachyos-rate-mirrors** (`github.com/CachyOS/rate-mirrors`) — ranks Arch and CachyOS mirrors for optimal `pacman` download speed.
- **systemd-boot-manager** (`github.com/CachyOS/systemd-boot-manager`) — auto-generates new `systemd-boot` entries; configured via `/etc/sdboot-manage.conf`.

## Community

Discord `discord.gg/cachyos-862292009423470592` and forum `discuss.cachyos.org`.
