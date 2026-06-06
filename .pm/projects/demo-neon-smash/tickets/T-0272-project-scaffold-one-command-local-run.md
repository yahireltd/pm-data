---
id: T-0272
title: Project scaffold & one-command local run
type: chore
state: ready
created: 2026-06-06T01:05:19Z
updated: 2026-06-06T01:06:47Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Running one documented command (e.g. `bun run dev`) starts the game in a browser with hot reload.
  - "The repo has the agreed layout: `client/` (Vite + TypeScript game) and `server/` (Bun + SQLite service)."
  - An on-screen frame-time / FPS counter is visible in dev builds and can be toggled off.
  - The README explains how to install and run it from scratch in under five steps.
out_of_scope:
  - Any gameplay — this ticket is only the runnable shell.
  - Production or cloud deploy (local run only, per scope-out).
code_anchors:
  - path: package.json
  - path: client/index.html
  - path: client/src/main.ts
    symbol: bootstrap
  - path: server/src/index.ts
    symbol: startServer
  - path: README.md
    lines: 1-40
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-1
  - setup
  - env
attention: null
backlog_status: confirmed_for_release
estimated_effort: S
source: charter
---

## Problem
We need a runnable shell before any gameplay: one command that starts the game in a browser, with the repo laid out the way the architecture assumes, so every later ticket has somewhere to land.

## Context
- Stack & layout: see ADR-001 (TypeScript + Canvas client, Bun + SQLite server, bundled with Vite).
- Closes the kickoff action item "stand up the dev environment + Claude conventions before Sprint 1" (meeting M-004).
- Part of milestone M1 · Playable core loop (MS-001).

## Design notes
- `client/` = Vite + TS game; `server/` = Bun HTTP + SQLite service. Vite dev-proxies `/api` to the Bun server so dev and prod behave the same (ADR-003).
- Add the FPS / frame-time overlay now — the 60fps acceptance criterion on later tickets needs it to be checkable (TS-001 action).
- Pin the charter + ADRs into the repo's agent context (`.claude/`) so fresh conversations land oriented.