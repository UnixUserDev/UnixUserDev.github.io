---
tags:
  - memory/tooling
created: 2026-10-19T12:36:01
source: agent
---

TinyServer (192.168.1.145, Win 11 Pro) is now the WTS — a disposable Windows box for proving commands, installs, MCP servers, skills and plugins work on Windows before shipping instructions. Driven from the Linux box by `wts` (~/.local/bin/wts, skill: wts): ps (pwsh 7.6), ps5 (Windows PowerShell 5.1), cmd, script, put/get/ls/cat, env, status, junk, snap/snaps/restore, reboot. SSH key auth with a pinned host key; `ssh tinyserver` also works. Junk lives in C:\wts and the reset model is a System Restore checkpoint (system+registry only, so app files can survive) plus `wts clean --yes`. Windows exit codes are the contract: a failing native tool's code wins, handled errors do not, and codes above 255 arrive truncated. There is no virtualization (SVM off in BIOS), so Windows Sandbox, Hyper-V, WSL2 and Docker cannot run — WSL is not installed. Defender stays on. Two box fixes were needed for real tests: npm's global prefix C:\Users\space\npm-global added to the user PATH, and the System Restore throttle set to 0 so checkpoints are not limited to one per day.

[[Inbox]]

[[Inbox]]
