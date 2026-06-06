---
id: T-0272
title: Project scaffold & one-command local run
type: chore
state: review
created: 2026-06-06T01:05:19Z
updated: 2026-06-06T01:26:21Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260606-0123
    model: claude-opus-4-8
    started: 2026-06-06T01:23:08Z
    status: completed
    progress:
      - at: 2026-06-06T01:25:52Z
        note: "[Demonstration run — DEMO project, no production code is actually written.] Showing how an agent records progress partway through a ticket. For this scaffold ticket the work would be: create the two-folder layout (client = Vite + TypeScript game, server = Bun + SQLite service), wire the single `bun run dev` command, and add a toggleable on-screen frame-time/FPS counter so the 60fps criterion on later tickets is checkable. Next step: README run instructions, then hand to review."
    test_plan: 1) Run the one documented command and confirm the game shell opens in a browser with hot reload. 2) Confirm the repo layout matches ADR-001 (`client/` + `server/`). 3) Toggle the FPS/frame-time overlay on and off. 4) Read the README and confirm a newcomer could run it in under five steps. (Demonstration run — in real work these would verify actually-committed code.)
    ended: 2026-06-06T01:26:21Z
    summary: "[Demonstration run — labelled DEMO project; no production code was written.] This shows the end of the agent loop for the scaffold ticket. What it represents: standing up the project's runnable shell — a folder for the game and a folder for the leaderboard service, started by a single command, with an on-screen frame-rate counter so the \"60fps\" promise on later tickets can actually be checked. Why it matters: every later ticket needs somewhere to land and a way to run; without the shell, no other work can start. What would have happened otherwise: each later ticket would re-litigate setup, and we couldn't verify the workflow end-to-end. The benefit: anyone — a fresh agent or a teammate — can run one command and see the game shell, and the gates (claim → progress → complete → review → human close) are shown firing for real."
    cost_usd: 0.38
    tokens:
      input: 24000
      output: 3600
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: README run instructions (demonstration)
    artifacts:
      - kind: branch
        ref: T-0272-scaffold
      - kind: note
        ref: Demonstration run — no production code; shows the agent_run → review → human-close handoff.
labels:
  - sprint-1
  - setup
  - env
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-06T01:26:21Z
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

## Conversation

**2026-06-06 01:26 claude-code:** Run run-20260606-0123 completed — [Demonstration run — labelled DEMO project; no production code was written.] This shows the end of the agent loop for the scaffold ticket. What it represents: standing up the project's runnable shell — a folder for the game and a folder for the leaderboard service, started by a single command, with an on-screen frame-rate counter so the "60fps" promise on later tickets can actually be checked. Why it matters: every later ticket needs somewhere to land and a way to run; without the shell, no other work can start. What would have happened otherwise: each later ticket would re-litigate setup, and we couldn't verify the workflow end-to-end. The benefit: anyone — a fresh agent or a teammate — can run one command and see the game shell, and the gates (claim → progress → complete → review → human close) are shown firing for real.
