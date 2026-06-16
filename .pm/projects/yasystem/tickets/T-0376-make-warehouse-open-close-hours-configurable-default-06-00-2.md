---
id: T-0376
title: Make warehouse open/close hours configurable (default 06:00–22:00)
type: chore
state: in_progress
created: 2026-06-15T15:37:11Z
updated: 2026-06-16T11:35:57Z
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
  - Warehouse open/close hours come from a single config value
  - calculateWorkingMinutes() uses it
  - Default 06:00-22:00 reproduces current behaviour
  - Same value is available to the visualiser endpoint for shading
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    symbol: calculateWorkingMinutes
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260615-1544
    model: claude-opus-4-8
    started: 2026-06-15T15:44:38Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-15T15:44:38Z
    progress:
      - at: 2026-06-15T15:45:28Z
        note: Code-complete on branch t0374-turnaround-visualiser. Added params warehouse.openHour (6) / warehouse.closeHour (22) in common/config/params.php, and YaRuns::warehouseHours() returning [open,close] from params with a 06:00–22:00 fallback. calculateWorkingMinutes() now reads the window via warehouseHours() instead of hardcoded 6/22. Default reproduces current behaviour exactly; the same accessor will feed the visualiser's non-open-hours shading. Not committed yet (policy).
    test_plan: 1. With no local override, turnaround eyes/calcs behave exactly as before (defaults 6/22). 2. Set warehouse.openHour/closeHour in params-local.php to e.g. 7/20 and confirm usable turnaround minutes shift accordingly (a borderline pair changes band). 3. YaRuns::warehouseHours() returns the configured pair, or [6,22] when params absent.
labels:
  - turnaround-visualiser
attention: null
version: 6
collaborators:
  - kind: human
    name: austin
---

## Problem

`YaRuns::calculateWorkingMinutes()` hardcodes the warehouse working window as 06:00–22:00. The turnaround calc (and so the visualiser's shaded non-open hours + usable-time maths) should use a configurable value.

## Work

Lift the `$workStartHour = 6` / `$workEndHour = 22` into a single configurable setting (params/config or a small settings row), read by `calculateWorkingMinutes()` and exposed to the visualiser endpoint so the timeline can shade non-open hours consistently. Default must reproduce today's behaviour exactly.
