---
tags:
  - memory/projects
created: 2026-10-16T12:21:24
source: agent
---

Verified on this CachyOS box (Node v26.11.1, no Bun): @opentui/core 0.6.6 installs with its native optional package @opentui/core-linux-x64 (glibc) and runs under plain Node. Requirements: package.json must set "type": "module", run with `node --experimental-ffi <file>`, import relative files with explicit .ts extensions, and avoid enums (ERR_UNSUPPORTED_TYPESCRIPT_SYNTAX in strip-only mode) — use `as const` objects/unions. TS type-stripping is on by default so no build step is needed; type-check only with `tsc --noEmit` + @types/node + erasableSyntaxOnly. Headless tests need no terminal: `createTestRenderer()` from `@opentui/core/testing` renders to an in-memory sink and `captureCharFrame()` returns frame text, so tuistory is not required. Real renderer verified working under a PTY.

[[Inbox]]
