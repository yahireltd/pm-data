---
id: T-0265
title: Cross-package typecheck (cli, mcp-server, comms) — not just the web build
type: chore
state: triaged
created: 2026-06-05T22:52:32Z
updated: 2026-06-22T16:16:22Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
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
    symbol: scripts.typecheck (root, add aggregate)
  - path: cli/package.json
    symbol: scripts.typecheck (missing — add)
  - path: docs/development.md
    symbol: pm-deploy script + deploy contract
  - path: AGENTS.md
    symbol: §9
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 3
---

## Problem

Today the server web build is the ONLY typecheck and it only covers web/ (+ the cli files it imports). Type errors in cli/, mcp-server/, comms/ surface at runtime or a manual check — two server-side build failures today were exactly this. A cross-package typecheck (local + CI/pre-deploy) tightens the feedback loop a lot.

## Context

Surfaced dogfooding on 2026-06-05.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep code check, adversarially re-confirmed) — genuinely unbuilt, and it's the foundation: build this FIRST.**

No root aggregate typecheck exists, `cli/` has no typecheck script, `pm-deploy` doesn't typecheck, and AGENTS.md §9 still says "there is no local typecheck." Two gotchas found and now added as acceptance criteria: (1) the repo does **not** typecheck clean today — `mcp-server/src/tools/record-outcome.ts:84` and the linter's tsconfig (`bun-types`) both fail in isolation, so "all five clean" needs those fixed; (2) the real gate is the server's `/usr/local/bin/pm-deploy`, separate from the doc snippet, and `linter` isn't in root workspaces so packages must be listed explicitly. **T-0395 (docs-drift gate) wants this gate to hook into — that's why it goes first.**
