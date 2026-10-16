---
tags:
  - memory/system
created: 2026-10-16T17:10:22
source: agent
---

On the CachyOS box, the AUR package `lmstudio-bin` was uninstalled on request, but the models were deliberately kept at `~/.lmstudio/models` (~16G): `lmstudio-community/gemma-4-E4B-it-GGUF` and `lmstudio-community/gemma-4-26B-A4B-it-QAT-GGUF`. So ~/.lmstudio still holds models + the `lms` CLI and 2.8G of extensions even though the desktop app is no longer installed. If a future task sees GGUF models with no LM Studio app, that is expected, not broken. Related: [[Inbox]]

[[Inbox]]
