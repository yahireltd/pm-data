---
id: T-0378
title: Vue turn-around timeline view (lanes, bars, overlaps)
type: feature
state: triaged
created: 2026-06-15T15:37:27Z
updated: 2026-06-15T15:39:38Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p2
assignee: null
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
agent_runs: []
labels:
  - turnaround-visualiser
attention: null
version: 2
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