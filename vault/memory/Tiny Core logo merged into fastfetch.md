---
tags:
  - memory/linux
created: 2026-10-16T04:08:24
source: agent
---

**Merged.** PR fastfetch-cli/fastfetch#2655 "Logo (Builtin): add Tiny Core Linux" landed on 2026-10-16 (merge commit 99785f54, merged by maintainer CarterLi), closing issue #2654. `src/logo/ascii/t/tinycore.txt` is live on `dev` and ships with the next release. The merge-commit CI run passed 24/24 jobs, including the emulated-VM architectures (armv7l, loong64, ppc64le, riscv64, s390x). Timeline: opened 01:39 CDT, reviewed in 25 minutes, merged 2h02m later.

Filed under the **UnixUserDev** account (fork `UnixUserDev/fastfetch`, branch `logo/tinycore`, base `dev`).

The logo is the project's own mark: a 21x10 sphere split diagonally, "tiny" upper-left / "micro" lower-right — the "o" of *core*. `$1` = `FF_COLOR_FG_BLUE` (matching the distro's own `ANSI_COLOR="0;34"`), `$2` = `FF_COLOR_FG_LIGHT_WHITE`. Registered in `src/logo/ascii/t.inc` between TileOS and Torizon OS.

The single review comment was "Only one entry is allowed here. The ID TinyCore (case insensitive) should only be used" — fixed by reducing the names array to one entry, `{ "TinyCore" }`. Matching is case-insensitive, so one name already covers both `ID=tinycore` and `NAME=TinyCore`; invented aliases are not wanted.

Upstream process for logos, learned the hard way:

- A **Logo Request issue must exist before** the PR, and the PR body must say `Closes #N`. Logo PRs without one are rejected by their template.
- The art must be under 50x20, use `$N` colour placeholders only, and carry no trailing spaces.
- You cannot set labels on a repository where you lack write access.

Motive: fastfetch shipped 544 built-in logos but none for Tiny Core, so it drew generic Tux. Tiny Core ranks #113 in DistroWatch's page-hit ranking — above roughly 104 distros that already have artwork.

[[Inbox]]
