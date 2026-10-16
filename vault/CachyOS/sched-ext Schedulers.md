---
title: sched-ext Schedulers
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-09-07
source: https://wiki.cachyos.org/configuration/sched-ext/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-09-07).

# sched-ext Schedulers

sched-ext (Extensible Scheduler Class) is a Linux kernel feature for implementing thread schedulers in **BPF** and loading them dynamically — you swap schedulers from userspace without building a new kernel.

Running any sched-ext scheduler temporarily **detaches the default kernel scheduler** (EEVDF or BORE); stopping it hands control back.

## Packages

Schedulers come from `scx-scheds` / `scx-scheds-git`:

```bash
# Stable branch + scx_loader and scxctl tools
sudo pacman -S scx-scheds scx-tools

# Bleeding edge + tools
sudo pacman -S scx-scheds-git scx-tools-git
```

`scx-scheds-git` may not include every experimental scheduler (some live in unmerged feature branches). Both `scxctl` and `scx_loader` need admin rights (`sudo`, or a polkit agent that prompts).

## Run one from a terminal

```bash
sudo scx_rusty
```

`CTRL + C` stops it and the default scheduler takes over again.

## `scxctl` (runtime control, needs a working polkit agent)

```bash
scxctl start --sched flash --mode gaming
scxctl stop
scxctl restore
scxctl switch --sched bpfland --mode gaming
scxctl start --sched cosmos --args="-c,75,-m,0-15"
scxctl switch --sched flash --args="-s,20000"
```

Arguments are comma-separated, e.g. `--args="-c,75,-m,0-15"` is argument,value pairs. Subcommands: `get`, `list`, `start`, `switch`, `stop`, `restart`, `restore`. `scxctl restore` returns to the default scheduler from the config file.

## `scx_loader` D-Bus interface

```
dbus-send --system --print-reply --dest=org.scx.Loader /org/scx/Loader org.scx.Loader.StartScheduler string:scx_rusty uint32:0
```

Mode numbers: **0** = default scheduler flags, **1** = Gaming, **2** = Power Saving, **3** = Low Latency, **4** = Server. Other methods: `StartSchedulerWithArgs`, `StopScheduler`, `RestoreDefault`, `SwitchScheduler`, `SwitchSchedulerWithArgs`, plus property reads `CurrentScheduler` / `SupportedSchedulers`.

## Setting a default scheduler at boot

```bash
sudo mkdir -p /etc/scx_loader
sudo cp /usr/share/scx_loader/config.toml /etc/scx_loader/config.toml
sudo nano /etc/scx_loader/config.toml
```

Set `default_sched = "scx_pandemonium"`, `default_mode = "Auto"`, then:

```bash
sudo systemctl enable --now scx_loader.service
```

Config lookup order: `/etc/scx_loader/config.toml` (preferred) → `/etc/scx_loader.toml` → `/usr/share/scx_loader/config.toml` → `/usr/share/scx_loader.toml`. Per-scheduler arguments go under a `[scheds.scx_name]` section.

> **Gotcha:** do not run `scx_loader.service` alongside `scx.service` — the loader starts but does nothing.

```bash
systemctl disable --now scx.service && systemctl enable --now scx_loader.service
systemctl restart scx_loader.service
```

The old `scx.service` (`SCX_SCHEDULER=`, `SCX_FLAGS=`) maps to `default_sched = "scx_lavd"` / `default_mode = "Auto"` plus `[scheds.scx_lavd]` with `auto_mode = ["--performance"]`. The Kernel Manager GUI handles the transition unless you had custom flags in `/etc/default/scx` — see [[CachyOS Kernel Manager]].

## Debugging

```bash
systemctl status scx_loader.service
journalctl -u scx_loader.service
journalctl -u scx_loader.service -b 0
```

`ananicy-cpp` usually coexists fine; disable it as a troubleshooting step for stalls: `systemctl disable --now ananicy-cpp`.

Power-profile switching (patched `power-profiles-daemon`): Power Saver → Power Save, Balanced → Auto, Performance → Gaming. The `game-performance` wrapper switches to Gaming on game launch and reverts on close.

## Benchmarking

```bash
sudo pacman -S cachyos-benchmarker
cachyos-benchmarker ~/cachyos-benchmarker/

sudo pacman -S schbench
schbench -m 2 -t 8 -r 60
```

Crash log:

```bash
journalctl --unit scx_loader.service --boot 0 > crash.log
```

## Schedulers documented on the page

`scx_beerland`, `scx_bpfland`, `scx_cake`, `scx_cosmos`, `scx_flash`, `scx_flow`, `scx_forge`, `scx_lavd`, `scx_pandemonium`, `scx_p2dq`, `scx_tickless`, `scx_rustland`, `scx_rusty`.

Each scheduler lists per-mode flags where it has them; use `scx_<schedulername> --help` for the real list. **No modes are listed for `scx_beerland` ("none for the moment"), `scx_cake`, `scx_forge`, `scx_rustland` or `scx_rusty`.** Example flags: bpfland Low Latency `-m performance -w`, Power Save `-s 20000 -m powersave -I 100 -t 100`; cosmos Gaming `-s 700 -S`; LAVD `--performance` / `--powersave` (plus `scx_lavd --autopower`); p2dq Gaming `--task-slice true -f --sched-mode performance`. **`scx_flow` has no Gaming/Power Save/Low Latency modes.**

Related: [[CachyOS Kernels]] (BORE/EEVDF/BMQ variants), [[CachyOS Kernel Manager]].
