---
title: QEMU User Session
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-11-24
source: https://wiki.cachyos.org/virtualization/qemu_user_session/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-11-24).

# QEMU User Session

Why run a VM in a **user session** instead of a root session: **networking** and **3D acceleration via translation**.

## Networking

A root session forbids the VM from forwarding ports and uses **NAT** instead — the VM can see the world, but the world cannot see the VM, it sees your PC. Fine in general, but you cannot connect *into* the VM from outside the machine (e.g. over ssh), because there is nothing to connect to.

## 3D acceleration

A root session forbids VMM from using the render node `/dev/dri/renderD128`, and cripples `qemu-cli` — a regular user can barely make use of root qemu.

## How to enable it

1. In the main **VMM** window, `File -> Add Connection...`.
2. In the `Hypervisor:` dropdown, select `QEMU/KVM user session`.
3. Create VMs as described in [[QEMU and VMM Setup]], in User mode rather than Root mode.

No packages, services, group changes, or config edits — the whole change is that one VMM connection setting.
