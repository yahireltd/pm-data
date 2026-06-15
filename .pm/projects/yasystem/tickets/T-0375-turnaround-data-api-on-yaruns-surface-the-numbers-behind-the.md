---
id: T-0375
title: Turnaround data API on YaRuns (surface the numbers behind the eyes)
type: feature
state: in_progress
created: 2026-06-15T15:37:05Z
updated: 2026-06-15T15:40:29Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A single method returns structured turnaround data (usable/working/load/unload mins, datetimes, weight, vol, band)
  - Existing run-planner eyes icon unchanged visually (orange 3-4h, red+flash <=3h)
  - Thresholds defined in exactly one place
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    symbol: checkTurnaroundNext
    lines: 2115-2150
  - path: common/models/YaRuns.php
    symbol: checkTurnaroundPrev
    lines: 2152-2188
  - path: common/models/YaRuns.php
    symbol: calculateWorkingMinutes
  - path: common/models/YaRuns.php
    symbol: getLoadUnloadMinutes
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260615-1540
    model: claude-opus-4-8
    started: 2026-06-15T15:40:29Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-15T15:40:29Z
labels:
  - turnaround-visualiser
attention: null
version: 4
---

## Problem

The turnaround maths is already correct and live (YaRuns::checkTurnaroundNext/Prev, YaRuns.php:2115–2188) but the methods only return an HTML icon + tooltip. The new visualiser needs the underlying numbers, and we don't want the thresholds duplicated in two places.

## Work

Extract the calculation into one method that returns structured data for a run→adjacent-run turnaround, e.g.:
`{ usable_mins, working_mins, load_mins, unload_mins, prev_end_dt, next_start_dt, weight_kg, vol_pct, band }`
where band = normal | tight | very_tight using the existing thresholds:
- usable > 240 min → normal
- 181–240 → tight
- ≤ 180 → very_tight

Have `checkTurnaroundNext/Prev` render their icon from this method so the run-planner eyes are unchanged and the visualiser link element consumes the same source of truth.

usable_mins = calculateWorkingMinutes(prevReturn → nextDispatch) − next load_mins − prev unload_mins. Return time = backAtBaseTime ?? endDateTime; dispatch = startDateTime.