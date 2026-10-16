---
tags:
  - memory/linux
created: 2026-10-16T01:45:32
source: agent
---

The OpenCode AppImage on this CachyOS box exports LD_LIBRARY_PATH=/tmp/.mount_OpenCo*/usr/lib into every child process of the agent session. System binaries then load the AppImage's bundled libraries instead of the system ones — notify-send dies with "symbol lookup error: notify-send: undefined symbol: notify_notification_get_activation_app_launch_context" because the AppImage ships an older libnotify.so.4. Fix: strip LD_LIBRARY_PATH/LD_PRELOAD from the child environment (env -u LD_LIBRARY_PATH, or subprocess env=). The systemd --user environment is NOT polluted, so timer- and service-launched processes are unaffected. Check with `systemctl --user show-environment`.

[[Inbox]]
