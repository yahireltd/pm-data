---
id: T-0378
title: Vue turn-around timeline view (lanes, bars, overlaps)
type: feature
state: in_progress
created: 2026-06-15T15:37:27Z
updated: 2026-06-15T15:59:08Z
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
  - Timeline with continuous time axis across day-1/day/day+1 and a perspective-date picker centring the middle day
  - Vehicle lanes fixed-position, grouped by type, empty lanes shown
  - Runs drawn as time-positioned bars with code + weight/vol
  - Load/unload shoulders drawn before/after each run bar, giving latest-start-loading and occupied-until times
  - Non-open warehouse hours shaded
  - Turnaround gaps coloured by band with usable time + load shown
  - Time-overlaps on a vehicle highlighted red
  - Read-only
out_of_scope: []
code_anchors:
  - path: backend/web/js/SketchPlanner.js
  - path: backend/views/route-planner/sketch-planner.php
  - path: common/models/YaRuns.php
    symbol: checkTurnaroundNext
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260615-1554
    model: claude-opus-4-8
    started: 2026-06-15T15:54:27Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-15T15:54:27Z
    progress:
      - at: 2026-06-15T15:59:08Z
        note: "Code-complete + committed (0c374237) on branch t0374-turnaround-visualiser. Vue timeline at /route-planner/turnaround: continuous 3-day axis with date picker, vehicle lanes grouped by type, run bars with load/unload shoulders, shaded non-open hours, turnaround connectors coloured by band, overlaps hatched red. Read-only. Not pushed yet (policy). Needs a real-server run-through (no app runtime locally)."
    test_plan: 1. Open /route-planner/turnaround — defaults to today; use the date picker / arrows to set a perspective day. 2. Confirm 3 day blocks (before/day/after), vehicles grouped 18T→3.5T with empty lanes shown, HIRE tag on non-fleet. 3. A run with a tight next-run shows an orange/red connector with usable time; hover for the breakdown. 4. Load (blue) shoulder sits before each bar, unload (purple) after — check the latest-start-loading reads correctly. 5. Non-open hours (outside 06:00–22:00) shaded. 6. Force two overlapping runs on one vehicle → red hatch.
labels:
  - turnaround-visualiser
attention: null
version: 5
---

## Problem

Render the visualiser as a visual timeline (Austin's steer) rather than a spreadsheet-style column grid, so overlaps, tight turnarounds and load/unload windows are immediately visible.

## Work

New Vue page (model on the sketch planner app):
- Perspective-date picker; the middle day is centred, day-before and day-after either side.
- Continuous time axis (x) across the 3-day window with day gridlines/labels (Day before / Day / Day after).
- Vehicle lanes (y) in fixed position, grouped by vehicle type; empty lanes shown.
- Each run drawn as a bar positioned by its dispatch→return datetime; bar shows run code + key figures (weight/vol). Reuse run-planner card styling/colours where it fits the bar.
- Load/unload shoulders: draw a load block immediately BEFORE the run bar (width = load_mins) and an unload block immediately AFTER (width = unload_mins). This makes the latest time to start loading (dispatch − load_mins) and the warehouse-occupied-until time (return + unload_mins) readable straight off the timeline.
- Non-warehouse-open hours shaded so usable turnaround time is visually obvious.
- Turnaround gap between consecutive bars coloured by band (blue normal / orange tight / red very-tight), with the usable time + weight/vol to turn around shown on/around the connector.
- Genuine time overlaps on one vehicle rendered as overlapping bars highlighted red.
- Read-only (no reassignment in v1).