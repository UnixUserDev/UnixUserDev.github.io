---
tags:
  - memory/preferences
created: 2026-10-19T11:04:11
source: agent
---

The user requires software to be distributed and installed via release artifacts, never plain source code. For BYOA this means built bundles attached to GitHub Releases (standalone tarball + npm-installable tarball) and install instructions that never say "git clone and run the source". Node cannot type-strip TypeScript inside node_modules, so a source tarball is not a runnable package.

[[Inbox]]
