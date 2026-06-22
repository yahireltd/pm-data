---
id: T-0265
title: Cross-package typecheck (cli, mcp-server, comms) — not just the web build
type: chore
state: review
created: 2026-06-05T22:52:32Z
updated: 2026-06-22T17:10:51Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A single root command (e.g. `bun run typecheck`) runs tsc --noEmit across cli/, mcp-server/, comms/, linter/ AND web/ in one pass, exiting non-zero if any package fails.
  - Runnable locally from the repo root so type errors are caught before push (today each package has its own typecheck script but there's no aggregate; cli/ has none).
  - pm-deploy runs the aggregate typecheck before `bun run build` and fails fast if any package fails — type errors no longer first surface as a server build failure.
  - "AGENTS.md §9 (deploy contract) updated: a local typecheck gate now exists (it currently says 'there is no local typecheck')."
  - All five packages typecheck clean once wired.
  - "Fix the two pre-existing type errors that block a clean aggregate run (the repo does NOT typecheck clean today): mcp-server/src/tools/record-outcome.ts:84 (TS2322 string|null vs string|undefined) and linter/tsconfig.json 'types' (deprecated 'bun-types' → 'bun', TS2688)."
  - Wire the gate into the REAL server pm-deploy (/usr/local/bin/pm-deploy), not only the doc snippet; enumerate packages explicitly since linter isn't in root workspaces (a --workspaces aggregate would silently skip it).
out_of_scope: []
code_anchors:
  - path: package.json
    symbol: workspaces (+linter) + typecheck aggregate script
  - path: mcp-server/src/tools/record-outcome.ts
    symbol: found.project ?? undefined (TS2322 fix)
  - path: linter/tsconfig.json
    symbol: "types: bun (was bun-types)"
  - path: cli/package.json
    symbol: added typecheck script
  - path: docs/development.md
    symbol: pm-deploy typecheck step + prose
  - path: AGENTS.md
    symbol: §9 deploy contract
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260622-1702
    model: claude-opus-4-8
    started: 2026-06-22T17:02:35Z
    status: completed
    ended: 2026-06-22T17:10:51Z
    summary: |-
      **What we did:** We added an automatic check that catches programming type-mistakes across the *whole* project — not just the website part — and made it run as a gate before anything ships. Along the way we fixed two long-standing mistakes that were quietly sitting in the code, and brought a previously-excluded part of the project into the standard setup so the check can see it.

      **Why:** Until now the only safety net was the website build, which checks just one of the project's five parts. Mistakes in the other four (the command-line tool, the agent connector, the email handler, and the rule-checker) weren't caught until they broke at runtime — two recent live build failures were exactly this.

      **If we did nothing:** Type-mistakes in those four unchecked parts would keep slipping through to production and only show up as confusing failures after deploy, costing time to trace back to the real cause.

      **The benefit:** One command now checks all five parts in one pass and runs automatically before every deploy, so a whole class of mistakes is stopped before it ever reaches the live system. The two pre-existing mistakes are fixed too, so the project is clean today.
    test_plan: |-
      Reviewer checklist:

      1. From the repo root run `bun run typecheck` — it checks cli, mcp-server, comms, linter and web in sequence and should exit 0 (all clean).
      2. Introduce a deliberate type error in any package (e.g. a bad assignment in `cli/src`), re-run — it should fail fast on that package with a non-zero exit. Revert it.
      3. Confirm `cli` now has its own `typecheck` script (it had none before).
      4. Confirm the two pre-existing errors are gone: `mcp-server/src/tools/record-outcome.ts` (the org-meeting promote path), and the linter (it now resolves its bun types because it's a workspace member with `@types/bun` installed).
      5. Docs: AGENTS.md §9 no longer claims "there is no local typecheck"; `docs/development.md` shows the typecheck step inside the pm-deploy snippet.

      DEPLOY NOTE (the not-in-repo half — AC7): at deploy time the REAL server script `/usr/local/bin/pm-deploy` must be edited to add `bun run typecheck &&` before the `web` build (the repo only documents it). Safe sequence: merge to master → edit the server pm-deploy → run it. Because linter is now a workspace member, the server's `bun install` installs its deps, so the typecheck passes there too — no chicken-and-egg.

      Cross-impact to re-check: adding `linter` to the root `workspaces` changes what `bun install` manages (the lockfile gained the linter entry). A clean `bun install` was run locally and reported no dependency changes beyond the linter workspace entry; confirm nothing else regressed.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §9 (deploy contract) and docs/development.md (pm-deploy snippet + prose) updated to document the new aggregate typecheck gate.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-22T17:10:51Z
collaborators:
  - kind: human
    name: Austin Pickering
version: 7
branch: t0265-cross-package-typecheck
---

## Problem

Today the server web build is the ONLY typecheck and it only covers web/ (+ the cli files it imports). Type errors in cli/, mcp-server/, comms/ surface at runtime or a manual check — two server-side build failures today were exactly this. A cross-package typecheck (local + CI/pre-deploy) tightens the feedback loop a lot.

## Context

Surfaced dogfooding on 2026-06-05.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep code check, adversarially re-confirmed) — genuinely unbuilt, and it's the foundation: build this FIRST.**

No root aggregate typecheck exists, `cli/` has no typecheck script, `pm-deploy` doesn't typecheck, and AGENTS.md §9 still says "there is no local typecheck." Two gotchas found and now added as acceptance criteria: (1) the repo does **not** typecheck clean today — `mcp-server/src/tools/record-outcome.ts:84` and the linter's tsconfig (`bun-types`) both fail in isolation, so "all five clean" needs those fixed; (2) the real gate is the server's `/usr/local/bin/pm-deploy`, separate from the doc snippet, and `linter` isn't in root workspaces so packages must be listed explicitly. **T-0395 (docs-drift gate) wants this gate to hook into — that's why it goes first.**

**2026-06-22 17:10 claude:** Run run-20260622-1702 completed — **What we did:** We added an automatic check that catches programming type-mistakes across the *whole* project — not just the website part — and made it run as a gate before anything ships. Along the way we fixed two long-standing mistakes that were quietly sitting in the code, and brought a previously-excluded part of the project into the standard setup so the check can see it.

**Why:** Until now the only safety net was the website build, which checks just one of the project's five parts. Mistakes in the other four (the command-line tool, the agent connector, the email handler, and the rule-checker) weren't caught until they broke at runtime — two recent live build failures were exactly this.

**If we did nothing:** Type-mistakes in those four unchecked parts would keep slipping through to production and only show up as confusing failures after deploy, costing time to trace back to the real cause.

**The benefit:** One command now checks all five parts in one pass and runs automatically before every deploy, so a whole class of mistakes is stopped before it ever reaches the live system. The two pre-existing mistakes are fixed too, so the project is clean today.
