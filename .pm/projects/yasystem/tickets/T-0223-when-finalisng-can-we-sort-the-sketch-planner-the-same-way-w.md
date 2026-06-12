---
id: T-0223
title: When finalisng can we sort the sketch planner the same way we sort the run planner on load- also introduce a sort runs on the sketch planner
type: feature
state: review
priority: p2
created: 2026-06-04T14:04:28Z
updated: 2026-06-12T01:15:58Z
project: yasystem
section: null
parent: null
children: []
order: 20480
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Run planner default order should mirror the sketch planner order in the view
  - add a sort to the runs on the sketch planenr
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1419
    model: claude
    started: 2026-06-04T14:19:24Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention:
  needed_by: human
  reason: Implemented & committed (8b37aef5); ready for human review/testing — test steps in the Conversation.
  since: 2026-06-04T18:03:11Z
branch: PickingSketchSalesDashFriday
version: 4
---

## Problem

_Suggested feature from meeting M-003._

When finalisng can we sort the sketch planner the same way we sort the run planner on load- also introduce a sort runs on the sketch planner

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5`.

1. The run-planner run query now `orderBy(sortOrder, id)` (`LogisticsController`), and finalize writes `ya_runs.sortOrder` = the sketch column index — so the run planner loads in the **same order as the sketch columns**.
2. New **"Sort runs"** dropdown on the sketch footer with the run planner's run-area sorts (Dispatch time, Return time, Start weight, End weight, Vehicle, Driver). Clicking the same option again toggles ascending/descending.

**To test:**
1. Finalize a sketch → open the run planner for that date → runs appear top-to-bottom in the **same order** as the sketch columns (left→right).
2. On the sketch click **Sort runs → Dispatch time** → columns reorder; click again → order reverses.
