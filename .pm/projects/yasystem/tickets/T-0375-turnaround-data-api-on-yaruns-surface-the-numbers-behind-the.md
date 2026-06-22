---
id: T-0375
title: Turnaround data API on YaRuns (surface the numbers behind the eyes)
type: feature
state: done
created: 2026-06-15T15:37:05Z
updated: 2026-06-22T16:23:17Z
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
  - "[x] A single method returns structured turnaround data (usable/working/load/unload mins, datetimes, weight, vol, band)"
  - "[x] Existing run-planner eyes icon unchanged visually (orange 3-4h, red+flash <=3h)"
  - "[x] Thresholds defined in exactly one place"
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
    progress:
      - at: 2026-06-15T15:45:25Z
        note: Code-complete on branch t0374-turnaround-visualiser. Extracted the turnaround maths into YaRuns::getTurnaroundData($direction, $otherRun) returning structured figures (usable/working/load/unload mins, datetimes, load+unload weight/vol, band). checkTurnaroundNext/Prev now render the existing eye icon from this data via renderTurnaroundIcon() — output unchanged. Thresholds live once in turnaroundBand() (normal >4h, tight 3-4h, very_tight <=3h). getTurnaroundData accepts an explicit run pair so the visualiser isn't limited to locked runs. Not committed yet (policy).
    test_plan: "1. On the run planner, hover the turnaround \"eyes\" on runs that previously showed blue/orange/red — colours, flashing (very tight) and tooltips (Next Run Start / Previous Run End + Load/Unload Weight/Vol) must be identical to before. 2. Confirm a run with no adjacent locked run shows no eye. 3. PHP: YaRuns::getTurnaroundData('next') returns the expected band for a known tight pair."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - turnaround-visualiser
attention: null
version: 9
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

## Conversation

**2026-06-22 16:23 — you**

done
