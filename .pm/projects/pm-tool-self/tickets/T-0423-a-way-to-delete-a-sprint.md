---
id: T-0423
title: A way to delete a sprint
type: feature
state: done
priority: p2
created: 2026-06-18T11:59:59Z
updated: 2026-07-14T12:30:38Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 102400
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A sprint can be deleted from the web Sprints/Phases view"
  - "[x] Agents can delete a sprint via MCP"
out_of_scope: []
code_anchors:
  - path: web/app/_actions/sprints.ts
    symbol: removeSprint
  - path: web/app/_components/PhasePlanner.tsx
    symbol: sprint trash control
  - path: mcp-server/src/tools/sprint-tools.ts
    symbol: pm_delete_sprint
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260714-1222
    started: 2026-07-14T12:22:35Z
    status: completed
    ended: 2026-07-14T12:22:51Z
    summary: "Backlog audit: this was already built — no new work needed, just confirmation. Sprints can be deleted today from the web (the bin control on the Phases planner, wired to the removeSprint action) and by agents (the pm_delete_sprint tool), shipped in commit 15eb2fe. This ticket sat in the backlog because the work was recorded under other tickets. Nothing to build; please confirm it does what you wanted and close."
    test_plan: "1. Open a project's Phases view, find a test sprint, click its bin/delete control → the sprint disappears. 2. Confirm a sprint with committed tickets behaves sensibly (tickets are not deleted with it). 3. Optional agent path: pm_delete_sprint removes one via MCP. If this matches what you wanted, close the ticket; if you wanted delete somewhere else too (e.g. the Sprints tab), send back saying where."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 9
---

We  can not delete sprints - i created a test sprint and now want. to remove it

## Conversation

**2026-07-14 12:22 claude-code:** Run run-20260714-1222 completed — Backlog audit: this was already built — no new work needed, just confirmation. Sprints can be deleted today from the web (the bin control on the Phases planner, wired to the removeSprint action) and by agents (the pm_delete_sprint tool), shipped in commit 15eb2fe. This ticket sat in the backlog because the work was recorded under other tickets. Nothing to build; please confirm it does what you wanted and close.

---

**2026-07-14 12:30 — you**

done
