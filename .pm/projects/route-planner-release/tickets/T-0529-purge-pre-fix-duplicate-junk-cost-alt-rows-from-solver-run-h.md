---
id: T-0529
title: Purge pre-fix duplicate/junk cost_alt rows from solver run history
type: chore
state: in_progress
created: 2026-07-08T17:08:29Z
updated: 2026-07-08T17:10:40Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - History page for Fri 10 Jul 2026 shows one card per real solve (full_luton primaries), no blank/duplicate-ID cards
  - Row counts logged before/after the delete (in ticket comment)
  - solver_runs table row count unchanged
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerPlanService.php:1801-1900
    note: the fixed cost_alt persistence (context)
  - path: backend/views/route-planner/history.php
    note: history cards render from solver_run_history
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1710
    model: claude-fable-5
    started: 2026-07-08T17:10:40Z
    status: in_progress
labels:
  - route-planner
  - data-cleanup
attention: null
version: 3
---

## Problem
Before the 2026-07-08 fix, every solve with a cost_alt wrote three solver_run_history rows: the primary (Manual + split label), a mislabelled "Manual" twin of the cost_alt (no split label, 0 contracts), and a blank-badge cost_alt row. Under the old seed-selection bug those cost_alt plans also always dropped MORE jobs, so the old cards are junk. Austin: "yes lets clear up those old bad plans".

## Fix (data-only, sandbox DB)
1. Delete `source='manual'` history rows whose solver_run_id also has a `source='cost_alt'` history row (the mislabelled twins — post-fix runs don't create these, so the join only matches old pairs).
2. Delete `source='cost_alt'` history rows created before 2026-07-08 16:00 (drop-heavy junk by construction under the old selection rule).

solver_runs rows are NOT touched — full replay data is preserved; only the history-page cards are curated. Post-fix solves already write exactly one primary + at most one Cost alt card.