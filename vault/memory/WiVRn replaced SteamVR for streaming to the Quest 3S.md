---
tags:
  - memory/projects
created: 2026-10-20T19:49:13
source: agent
---

SteamVR was dropped on this box because vrcompositor kept segfaulting (same instruction pointer each time) and produced "Your host PC has stopped responding" on the Quest 3S. Replaced with WiVRn (Monado-based), which does not route the stream through vrcompositor.

Setup that works:
- Server: Flathub app io.github.wivrn.wivrn 26.9, installed into the USER flatpak installation (the system and user flathub remotes both exist, so installs need an explicit --user or --system).
- Client: WiVRn-release.apk (GitHub release v26.9) sideloaded with adb, package org.meumeu.wivrn.github. Quest 3S over USB reports as 340YC20G8602DJ; needs Developer Mode + USB debugging and the Allow dialog accepted.
- Firewall (ufw): allow 9757/udp, 9757/tcp and 5353/udp, otherwise discovery and streaming silently fail.
- Pairing: run the dashboard (flatpak run io.github.wivrn.wivrn), which starts wivrn-server and prints a 6-digit PIN in its server_logs file.
- Audio: WiVRn exposes a PipeWire sink named wivrn.sink. It stays SUSPENDED while another sink is default, which is what "no audio in the headset" looks like. pactl set-default-sink wivrn.sink, or just use the desk headphones.

Also relevant: Cloudflare WARP (warp-svc) was rewriting routes every few seconds, which made NetworkManager re-DHCP wlan0 every 30-45s and tore down the streaming socket. It is now disabled permanently.

[[Inbox]]

[[Inbox]]
