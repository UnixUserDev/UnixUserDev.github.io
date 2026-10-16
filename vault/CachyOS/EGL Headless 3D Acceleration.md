---
title: EGL Headless 3D Acceleration
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-01-28
source: https://wiki.cachyos.org/virtualization/egl_headless/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-01-28).

# EGL Headless (3D acceleration)

For guests that use `OpenGL` — **Linux**, **BSD**, **Android** — you get much better performance than software paravirtualization by translating calls from the VM straight to your GPU and back, **without** dedicating GPU hardware to the VM (i.e. no passthrough).

## Setup

Prerequisite: the VM must be eligible for 3D acceleration and running in **User Session** mode — see [[QEMU User Session]].

1. Open the VM in VMM and follow the wiki's picture to add EGL Headless.
2. In `Video Virtio`, tick `3D Acceleration`.

That is the entire change. The page lists **no** packages, systemd services, config paths, or kernel parameters — it is purely a VMM UI toggle.
