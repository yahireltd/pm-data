---
id: T-0194
title: Milestones MCP tool
type: feature
state: review
priority: p2
created: 2026-06-03T23:06:12Z
updated: 2026-06-04T01:27:15Z
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
  needed_by: human
  reason: Backfill confirmed — MS-007/008/009 created + set to hit. Ready to close.
  since: 2026-06-04T01:27:15Z
---

claude can not update milestones as there is no tool to do it. Add this tool and any other helpful tools so claude can do what is necessary

## Conversation

**2026-06-04 01:00 claude code:** Run run-20260603-2307 completed — Added milestone MCP tools (pm_create_milestone / list / get / update_state / add+remove criterion) so an agent can create + backfill milestones.

---

**2026-06-04 01:05 — Austin Pickering**

* we havent backfilled the milestones to prove it worked - try that then we can review again

**2026-06-04 01:10 claude-code:** Backfilled the milestones with the new pm_create_milestone tool to prove T-0194: created MS-007, MS-008, MS-009 (project pm-tool-self), each set to `hit` via pm_update_milestone_state. The tool works end-to-end (create + list + update_state). Ready for re-review. Separately fixed the send-back bug you hit (review->in_progress now; review->ready was illegal).

**2026-06-04 01:27 claude-code:** Yes — all the milestones we'd flagged as un-recorded are backfilled: **MS-007** Hosted & remotely drivable, **MS-008** Multi-user access control, **MS-009** Two-way comms — each created via pm_create_milestone and set to `hit`. The project now has 9 milestones (MS-001–006 already existed from earlier work). So every threshold from the Jun 2–3 reconciliation is now a milestone. If you want one for the SPR-022 batch too, say the word. Re-requesting review.
