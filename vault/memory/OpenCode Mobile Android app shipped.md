---
tags:
  - memory/projects
created: 2026-10-22T11:20:41
source: agent
---

The OpenCode mobile web client now has a native Android wrapper: `app.opencode.mobile` 1.0, a single-Activity WebView app built without Gradle (aapt2 / javac / d8 / apksigner, script at opencode-mobile/android/build.sh). It lives on the Samsung Galaxy A54 5G and remembers the login cookie across restarts. Since 1.1 the gateway address is not hardcoded: build.sh bakes the box's current LAN IP into a generated Defaults.java (override with BASE_URL=), and when a load fails the app sweeps its own /24 for port 10999, identifies the gateway by its unauthenticated /__health JSON, and switches to it. That matters because the box is on DHCP and moved from .250 to .182, which silently broke the old build. The trap worth remembering: WebView.loadUrl() accepts a bare IP like "192.168.1.182" and then does nothing at all - no page events, no error, no request - so a missing scheme looks exactly like a dead server. Two hard-won facts: d8 in build-tools 34 crashes on non-static inner classes compiled by JDK 21 (use static nested classes), and the host firewall must allow 10999 from the LAN or the phone sees a dead server.

[[Inbox]]
