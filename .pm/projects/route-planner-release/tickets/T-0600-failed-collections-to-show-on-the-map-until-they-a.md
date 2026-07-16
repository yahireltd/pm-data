---
id: T-0600
title: Show open failed collections on the sketch map until completed
type: feature
state: in_progress
priority: p1
created: 2026-07-16T17:01:32Z
updated: 2026-07-16T18:01:56Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 7168
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Open failed collections appear on the sketch map with a visually distinct marker and an info popup (contract, postcode, weight, days outstanding)
  - They appear on every planning date until the collection is completed, then disappear
  - Map performance unaffected on a normal day (failed set is small; no extra geocoding round-trips for cached addresses)
out_of_scope: []
code_anchors:
  - path: backend/web/js/SketchPlanner.js
    note: leaflet map rendering — add failed-collection marker layer
  - path: backend/controllers/RoutePlannerController.php
    note: sketch view data / geocode endpoint — supply failed collections for date
  - path: common/models/YaRunContracts.php
    note: failed-collection status semantics (rc_status / failed flag used by getStopStatus)
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1801
    model: claude-fable-5
    started: 2026-07-16T18:01:56Z
    status: in_progress
labels:
  - route-planner
  - ui
  - release-blocker
attention: null
version: 5
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Failed collections (attempted but not completed on an earlier day) are invisible while planning. Logistics want them ON the sketch map — every day until they're done — so when a route passes near one they can opportunistically add it to that route.

## Fix shape
- Server: for the plan date, fetch collections in a failed/not-yet-completed state (regardless of original date), with lat/lng (geocode via the existing address cache) and contract details.
- Map: render them as distinct markers (e.g. red warning pins) with a popup (contract no, postcode, weight/volume, days outstanding). Display-only for v1 — adding one to a route is done by the normal search/add flow; the map just makes them visible.
- They disappear from the map once completed.