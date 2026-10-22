---
tags:
  - memory/projects
created: 2026-10-22T11:20:41
source: agent
---

The OpenCode mobile web client now has a native Android wrapper: `app.opencode.mobile` 1.0, a single-Activity WebView app built without Gradle (aapt2 / javac / d8 / apksigner, script at opencode-mobile/android/build.sh). It lives on the Samsung Galaxy A54 5G, reaches the gateway at http://192.168.1.250:10999 over the LAN, and remembers the login cookie across restarts. Two hard-won facts: d8 in build-tools 34 crashes on non-static inner classes compiled by JDK 21 (use static nested classes), and the host firewall must allow 10999 from the LAN or the phone sees a dead server.

[[Inbox]]
