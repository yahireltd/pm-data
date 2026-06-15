---
id: T-0383
title: Take our own vehicles off-road for a day (maintenance) — button on run planner
type: feature
state: triaged
created: 2026-06-15T19:07:56Z
updated: 2026-06-15T19:16:19Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Off-road button on the run planner opens a modal of our owned vehicles
  - Ticking a vehicle takes it off road for that day (single-day vehicles_off_road row); unticking removes it
  - Off-road vehicles no longer appear in the assign-vehicle dropdown for that day
  - Vehicles off-road via a multi-day range are shown but not togglable from this single-day control
out_of_scope: []
code_anchors:
  - path: backend/controllers/LogisticsController.php
    symbol: actionToggleVehicleOffRoad
  - path: backend/views/logistics/run-planner.php
  - path: common/models/VehiclesOffRoad.php
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - run-planner
attention: null
version: 2
---

## Problem

We regularly take our own vehicles off the road for a day (maintenance). There was no quick way to do this from the run planner — off-road vehicles (vehicles_off_road) existed but had no in-page control.

## Work

Run planner: new "Off-road vehicle" button next to Hire vehicles / Add Memo-Break, opening a modal that lists our owned vehicles (ourVehicle=1) with a tick to take each off the road for the selected day. Backed by two endpoints:
- vehicle-off-road-list (GET ?date) — owned vehicles + off_road / range_locked flags for the date
- toggle-vehicle-off-road (POST {vehicleID, date}) — creates/removes a single-day vehicles_off_road row (dateFrom=dateTo=date)

A vehicle off-road via a broader date range is shown as "Off-road (date range)" and isn't togglable from this single-day control (avoids silently splitting a range). The assign-vehicle dropdown already excludes vehicles covered by an off-road row for the day, so off-road vehicles drop out automatically.

## Possible follow-up

Mirror on the sketch planner / ensure the sketch solver excludes off-road vehicles.

## Conversation

**2026-06-15 19:16 claude-code:** Off-road button added to the sketch planner too (parity with run planner), commit 28ac03ae. Off-road vehicles already drop out of the assign-vehicle dropdown for the day.

Open item: couldn't verify whether the SKETCH SOLVER excludes off-road vehicles — its fleet builder `RoutePlanner::getActiveFleetOR()` (common\components\RoutePlanner) is imported/called but the file `common/components/RoutePlanner.php` is not present on this branch (same untracked-on-server pattern as the SalesScores files). Need that file to confirm/patch the solver to skip vehicles_off_road for the date. Flagging rather than guessing.
