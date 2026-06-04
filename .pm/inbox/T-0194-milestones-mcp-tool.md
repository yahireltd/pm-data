---
id: T-0194
title: Milestones MCP tool
type: feature
state: review
priority: p2
created: 2026-06-03T23:06:12Z
updated: 2026-06-04T01:10:53Z
project: null
section: null
parent: null
children: []
order: 4096
reporter: null
assignee:
  kind: agent
  name: claude code
acceptance_criteria:
  - claude can create a milestone (this is for the backfill of what has been done)
  - any other tools for the backfill need to be created
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2307
    model: claude code
    started: 2026-06-03T23:07:02Z
    status: completed
    summary: Added milestone MCP tools (pm_create_milestone / list / get / update_state / add+remove criterion) so an agent can create + backfill milestones.
    ended: 2026-06-04T01:00:48Z
    test_plan: Restart the MCP, then pm_create_milestone for pm-tool-self; confirm it appears and passes pm-lint.
labels: []
attention:
  needed_by: agent
  reason: I have left a comment- we need to try the backfill to see if it worked
  since: 2026-06-04T01:06:16Z
---

claude can not update milestones as there is no tool to do it. Add this tool and any other helpful tools so claude can do what is necessary

## Conversation

**2026-06-04 01:00 claude code:** Run run-20260603-2307 completed — Added milestone MCP tools (pm_create_milestone / list / get / update_state / add+remove criterion) so an agent can create + backfill milestones.

---

**2026-06-04 01:05 — Austin Pickering**

* we havent backfilled the milestones to prove it worked - try that then we can review again

**2026-06-04 01:10 claude-code:** Backfilled the milestones with the new pm_create_milestone tool to prove T-0194: created MS-007, MS-008, MS-009 (project pm-tool-self), each set to `hit` via pm_update_milestone_state. The tool works end-to-end (create + list + update_state). Ready for re-review. Separately fixed the send-back bug you hit (review->in_progress now; review->ready was illegal).
