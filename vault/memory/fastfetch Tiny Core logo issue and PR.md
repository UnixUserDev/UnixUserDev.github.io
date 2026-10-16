---
tags:
  - memory/linux
created: 2026-10-16T01:40:23
source: agent
---

**Status: MERGED.** PR #2655 landed upstream in fastfetch-cli/fastfetch on 2026-10-16T08:41:13Z, and issue #2654 auto-closed on the merge — Tiny Core's logo now ships in fastfetch.

Upstream contribution filed under the UnixUserDev GitHub account: fastfetch issue #2654 "[LOGO] Tiny Core Linux" and PR #2655 "Logo (Builtin): add Tiny Core Linux" (fork UnixUserDev/fastfetch, branch logo/tinycore, base dev). The PR adds src/logo/ascii/t/tinycore.txt (21x10 sphere with a diagonal split, "tiny" upper-left / "micro" lower-right, $1=BLUE matching the distro's ANSI_COLOR="0;34", $2=LIGHT_WHITE) and registers names "Tiny Core Linux"/"TinyCore"/"Tiny Core"/"MicroCore" in src/logo/ascii/t.inc between TileOS and Torizon OS. Motive: fastfetch had 544 built-in logos but no Tiny Core, so it drew generic Tux; Tiny Core ranks #113 on DistroWatch, above several distros that do have art. Upstream requires a Logo Request issue to exist before a logo PR, and the PR must say `Closes #N`. CI on the PR was gated as action_required until a maintainer approved the first-time contributor's workflow run; that approval came, and the PR merged roughly two hours after filing. Note: labels cannot be set on a repo you lack write access to.

[[Inbox]]
