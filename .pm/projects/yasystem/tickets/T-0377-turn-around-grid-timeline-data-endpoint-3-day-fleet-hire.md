---
id: T-0377
title: Turn-around grid/timeline data endpoint (3-day fleet + hire)
type: feature
state: in_progress
created: 2026-06-15T15:37:19Z
updated: 2026-06-15T15:46:45Z
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
  - Endpoint takes a perspective date, returns day-1/day/day+1 window + warehouse hours
  - Vehicles = active fleet + hire pool, grouped by type, ignore-flagged excluded, empty rows kept
  - Each run carries absolute dispatch/return datetimes + card data (code, hours, start/end weight, vol, status, lock/pushed)
  - Each run carries its own load_mins and unload_mins
  - Turnaround links between consecutive runs incl. across days
  - Time-overlaps between a vehicle's runs flagged
out_of_scope: []
code_anchors:
  - path: backend/controllers/RoutePlannerController.php
  - path: common/models/Vehicles.php
    symbol: getActiveFleet
  - path: common/models/YaRuns.php
    symbol: getLoadUnloadMinutes
  - path: backend/controllers/LogisticsController.php
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260615-1546
    model: claude-opus-4-8
    started: 2026-06-15T15:46:45Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-15T15:46:45Z
labels:
  - turnaround-visualiser
attention: null
version: 4
---

## Problem

The Vue timeline needs one endpoint that returns everything for a perspective date.

## Work

New controller action: given a perspective date, return JSON:
- window: day-1, day, day+1 boundaries + warehouse open/close (from the config ticket) so the client can shade non-open hours
- vehicles: active fleet + hire pool, grouped by vehicle type, honouring the ignore-vehicle flag (once merged; no-op if absent), empty rows included (vehicles with no runs in the window)
- per vehicle: runs across the 3 days, each with absolute dispatch/return datetimes (for timeline positioning), plus card data — run code, hours, start/end weight, vol, status, locked/pushed
- per run: `load_mins` and `unload_mins` (this run's own load/unload time via getLoadUnloadMinutes) so the client can draw load/unload shoulders and derive the latest-start-loading time (dispatch − load_mins) and warehouse-occupied-until (return + unload_mins)
- turnaround links between consecutive runs (incl. across day boundaries) from the turnaround data API
- overlap flag where a vehicle has two runs whose time ranges intersect

Reuse Vehicles::getActiveFleet() and the hire-pool logic in LogisticsController.