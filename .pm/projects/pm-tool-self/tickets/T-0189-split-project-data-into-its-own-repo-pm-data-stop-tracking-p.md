---
id: T-0189
title: Split project data into its own repo (pm-data); stop tracking .pm/ in code
type: chore
state: review
created: 2026-06-03T22:23:09Z
updated: 2026-06-03T22:23:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Code repo no longer tracks .pm/ (gitignored); instance data lives in its own repo (pm-data) at PM_ROOT
  - Production build works wherever the repo is checked out (next.config derives repo root)
  - Local .pm stays on disk as a dev sandbox; data edits never appear as code commits
out_of_scope: []
code_anchors:
  - path: .gitignore
    commit: "4e56648"
  - path: web/next.config
    commit: a2db99d
    note: outputFileTracingRoot portable
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2223
    started: 2026-06-03T22:23:09Z
    status: completed
    ended: 2026-06-03T22:23:09Z
    summary: Retroactive record. Shipped across 4e56648/55403f4/a2db99d on 2026-06-03; live. The code/data repo split that made the remote MCP possible (see TS-002).
    test_plan: On the server, edit a ticket via the web/MCP → the change lands in the pm-data repo, not the code repo; `git status` in the code checkout shows no .pm changes.
labels:
  - backfill
  - infra
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-03T22:23:09Z
---

## Problem
Instance data (projects, tickets, config) was tracked inside the code repo, so data edits showed up as code commits and the two couldn't evolve independently.
## Change
`.pm/` is now gitignored; instance data lives in its own repo (`yahireltd/pm-data`) at `PM_ROOT` on the server. Files stay on disk locally as a dev sandbox. `next.config` `outputFileTracingRoot` now derives the repo root so the production build works wherever it's checked out.
## Note
Retroactive record of commits `4e56648`, `55403f4`, `a2db99d` (2026-06-03). Part of tech-session TS-002 (deployment).

## Conversation

**2026-06-03 22:23 claude-code:** Run run-20260603-2223 completed — Retroactive record. Shipped across 4e56648/55403f4/a2db99d on 2026-06-03; live. The code/data repo split that made the remote MCP possible (see TS-002).
