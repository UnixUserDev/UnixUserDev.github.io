---
tags:
  - memory/projects
created: 2026-10-18T04:54:54
source: agent
---

For the BYOA harness sandbox (bubblewrap 0.13.1 at /usr/bin/bwrap, unprivileged userns enabled, uid 1000) these recipes were executed and verified:

Base: bwrap --ro-bind / / --dev /dev --proc /proc --tmpfs /tmp --bind "$WORK" "$WORK" --chdir "$WORK" --die-with-parent --new-session --setenv HOME "$WORK" /bin/sh -c '...'

Gotchas found by testing, not docs:
1. With --ro-bind / /, any bind whose DESTINATION does not exist fails hard: "bwrap: Can't create file /mnt/extra: Read-only file system" — and --dir does NOT fix it. Fixes that work: put --tmpfs on the parent first (--tmpfs /mnt then --ro-bind SRC /mnt/extra), pre-create the dir on the host, or skip --ro-bind / / and build a skeleton root (/usr /etc /bin /lib /lib64 + --dir /opt).
2. --unshare-all gives a netns where lo is ALREADY up with 127.0.0.1/8, so local bind/connect works and outbound is dead. Do not try "ip link set lo up" — it fails with RTNETLINK Operation not permitted even at uid 0 inside the userns.
3. --unshare-all --share-net exposes the HOST netns (reaches host 127.0.0.1 but also LAN/internet). There is no "host loopback only" flag. To reach exactly one service, keep --unshare-all and bind only that service's unix socket directory — AF_UNIX works across net isolation.
4. Missing --proc /proc breaks tools that read /proc/self/*. Missing /etc/resolv.conf breaks DNS only when /etc is shadowed; re-bind resolv.conf + hosts to restore.
5. uid mapping: --unshare-all alone still shows uid=1000; --unshare-user --uid 0 --gid 0 gives uid=0 inside.

[[Inbox]]

[[Inbox]]
