---
id: T-0265
title: Cross-package typecheck (cli, mcp-server, comms) — not just the web build
type: chore
state: triaged
created: 2026-06-05T22:52:32Z
updated: 2026-06-08T18:55:25Z
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
---

## Problem

Today the server web build is the ONLY typecheck and it only covers web/ (+ the cli files it imports). Type errors in cli/, mcp-server/, comms/ surface at runtime or a manual check — two server-side build failures today were exactly this. A cross-package typecheck (local + CI/pre-deploy) tightens the feedback loop a lot.

## Context

Surfaced dogfooding on 2026-06-05.