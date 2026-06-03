---
id: T-0188
title: Inbox listed closed tickets as untriaged + sidebar Tickets badge double-count
type: bug
state: done
created: 2026-06-03T22:23:08Z
updated: 2026-06-03T22:28:52Z
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
  - Closed (done/wontfix/duplicate) inbox tickets no longer show as untriaged in /inbox, the dashboard Inbox panel, or the sidebar Inbox badge
  - Sidebar Tickets badge counts each inbox ticket exactly once
out_of_scope: []
code_anchors:
  - path: web/app
    commit: a3d619a
    note: inbox list/count, dashboard Inbox panel, sidebar Inbox+Tickets badges
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
    summary: Retroactive record. Shipped in commit a3d619a on 2026-06-03; already in production. Back-fills the ticket so the fix is visible in the record.
    test_plan: Close an inbox (inbound-email) ticket → it leaves /inbox, the dashboard Inbox panel and the sidebar Inbox badge; confirm the sidebar Tickets badge counts it once.
labels:
  - backfill
attention: null
---

## Problem
Inbox surfaces listed everything in `.pm/inbox/` regardless of state, so a closed (done/wontfix/duplicate) inbound-email ticket still showed as "untriaged". The sidebar **Tickets** badge also double-counted inbox tickets (`loadAllTickets` already spans the inbox dir).
## Fix
Filter closed states out of the `/inbox` list + count, the dashboard Inbox panel and the sidebar Inbox badge; fix the Tickets badge double-count.
## Note
Retroactive record of work shipped in commit `a3d619a` (2026-06-03); surfaced as a side-quest of the inbound-email build (T-0177).

## Conversation

**2026-06-03 22:23 claude-code:** Run run-20260603-2223 completed — Retroactive record. Shipped in commit a3d619a on 2026-06-03; already in production. Back-fills the ticket so the fix is visible in the record.

---

**2026-06-03 22:28 — you**

done as expected
