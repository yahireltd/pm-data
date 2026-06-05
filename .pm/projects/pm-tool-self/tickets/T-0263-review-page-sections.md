---
id: T-0263
title: Review page sections
type: feature
state: done
priority: p2
created: 2026-06-05T22:28:16Z
updated: 2026-06-05T23:55:18Z
project: pm-tool-self
section: null
parent: null
children: []
order: 86016
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] we can seee each project that needs review on the view and the dropdown acts as a show only filter"
out_of_scope: []
code_anchors:
  - path: web/app/review/page.tsx
    symbol: needsReview
  - path: web/app/review/ReviewFilters.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2229
    model: claude
    started: 2026-06-05T22:29:13Z
    status: completed
    summary: Fixed the Review page so it actually shows what needs a human look. Before, it only listed tickets that carried an explicit "attention" flag — so when agents finished work and parked it for review, you'd land on the page and see "nothing needs doing" even though things were waiting. Now it surfaces every ticket an agent has handed back for review (plus anything flagged), grouped by project so you can see at a glance which projects need you. And the project dropdown is now a proper show-only filter (with an "All projects" default) instead of quietly defaulting to whatever project you last opened and hiding everything else.
    ended: 2026-06-05T23:49:34Z
    test_plan: Land on /review with several tickets in the review state across different projects → they all appear, grouped into per-project sections with a count each. Pick a project in the dropdown → only that project's items show; "All projects" shows everything. With nothing in review → a clear empty state instead of a blank page. Build green + deployed (3a8cec5).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

There is flags for reveiew when i land on that page i see nothing needs doing. the project dropdown should be a filter rather than a show only.

## Conversation

**2026-06-05 23:49 claude:** Run run-20260605-2229 completed — Fixed the Review page so it actually shows what needs a human look. Before, it only listed tickets that carried an explicit "attention" flag — so when agents finished work and parked it for review, you'd land on the page and see "nothing needs doing" even though things were waiting. Now it surfaces every ticket an agent has handed back for review (plus anything flagged), grouped by project so you can see at a glance which projects need you. And the project dropdown is now a proper show-only filter (with an "All projects" default) instead of quietly defaulting to whatever project you last opened and hiding everything else.

---

**2026-06-05 23:55 — you**

Precisely as scoped
