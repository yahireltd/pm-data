---
id: T-0244
title: re-order the deliver tabs of project
type: feature
state: in_progress
priority: p2
created: 2026-06-05T19:01:36Z
updated: 2026-06-05T19:14:44Z
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
    summary: The tabs in a project's Deliver section now run Sprints, Backlog, List, Board — the order you asked for — instead of leading with the Board. So the views you use most to drive delivery come first. The sidebar's Deliver entry follows the same order.
    ended: 2026-06-05T19:12:01Z
    test_plan: "Open any project and look at the Deliver tabs (and the Deliver group in the left sidebar): they read Sprints, Backlog, List, Board, left to right. Each still opens its view; the Board is still the project's default landing page."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: agent
  reason: works fine but need to have sprint as default selected tab
  since: 2026-06-05T19:14:44Z
---

1. sprints
2. backlog
3. list
4. board

## Conversation

**2026-06-05 19:12 claude:** Run run-20260605-1905 completed — The tabs in a project's Deliver section now run Sprints, Backlog, List, Board — the order you asked for — instead of leading with the Board. So the views you use most to drive delivery come first. The sidebar's Deliver entry follows the same order.
