---
id: T-0190
title: "Admin: on-demand back up data to git (push .pm → pm-data)"
type: feature
state: review
created: 2026-06-03T22:23:09Z
updated: 2026-06-03T22:23:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Admins see a Data backup card on /me with the count of unpushed .pm changes
  - On demand it commits+pushes the pm-data repo, attributed to the acting admin
  - Guarded to only ever touch a repo whose origin is pm-data (safe no-op in local dev)
out_of_scope: []
code_anchors:
  - path: web/app/me
    commit: bfbcdd7
    note: admin Data backup card
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
    summary: Retroactive record. Shipped in bfbcdd7 on 2026-06-03; live. On-demand git backup for admins, complementing the 10-min timer.
    test_plan: As an admin on /me, make a .pm change then click backup → the pm-data repo gets a commit attributed to you; as a non-admin the card is absent.
labels:
  - backfill
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-03T22:23:09Z
---

## Problem
Data was only backed up by a 10-min systemd timer; an admin had no way to flush pending changes on demand.
## Change
A **Data backup** card on `/me` (admins only) shows how many `.pm/` changes aren't yet pushed and pushes them on demand, attributed to the acting admin. Guarded to only ever touch a repo whose origin is `pm-data`, so it's a safe no-op in local dev.
## Note
Retroactive record of commit `bfbcdd7` (2026-06-03). Complements TS-002.

## Conversation

**2026-06-03 22:23 claude-code:** Run run-20260603-2223 completed — Retroactive record. Shipped in bfbcdd7 on 2026-06-03; live. On-demand git backup for admins, complementing the 10-min timer.
