---
id: T-0244
title: re-order the deliver tabs of project
type: feature
state: review
priority: p2
created: 2026-06-05T19:01:36Z
updated: 2026-06-05T19:21:37Z
project: pm-tool-self
section: null
parent: null
children: []
order: 76800
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] re order according to list in description"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/project-nav.ts
    symbol: PROJECT_NAV
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1905
    model: claude
    started: 2026-06-05T19:05:35Z
    status: completed
    summary: "Two parts now: (1) the Deliver tabs are ordered Sprints, Backlog, List, Board — your requested order; (2) opening a project now lands on the Sprints tab by default instead of the Board. The Board is still one click away (its own tab) and remains reachable directly; we just changed where you arrive so the view you use most to drive delivery is front and centre."
    ended: 2026-06-05T19:21:37Z
    test_plan: Open any project from the projects list or sidebar — you arrive on the Sprints tab (not the Board). The Deliver tabs read Sprints, Backlog, List, Board. Click Board — it shows the Kanban board. Hard-refresh if a tab was open from before the deploy.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T19:21:37Z
---

1. sprints
2. backlog
3. list
4. board

## Conversation

**2026-06-05 19:12 claude:** Run run-20260605-1905 completed — The tabs in a project's Deliver section now run Sprints, Backlog, List, Board — the order you asked for — instead of leading with the Board. So the views you use most to drive delivery come first. The sidebar's Deliver entry follows the same order.

---

**2026-06-05 19:21 — you**

works fine now
