---
id: T-0611
title: Very strong warning (typed confirmation) when finalising TODAY's plan; server-side guard
type: feature
state: ready
created: 2026-07-17T13:09:41Z
updated: 2026-07-17T13:10:07Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: session
assignee: null
acceptance_criteria:
  - Finalising a FUTURE date behaves exactly as before (no new friction)
  - Finalising TODAY shows the red modal; confirm stays disabled until the user types TODAY; cancel backs out cleanly
  - Bypassing the UI (direct POST without confirm_today=1) is rejected server-side with a clear error for today's date
  - The same-day finalise is written to sketch-planner.log with user + sketch id
out_of_scope: []
code_anchors:
  - path: backend/web/js/SketchPlanner.js
    note: finalize() flow + confirm dialog machinery
  - path: backend/controllers/RoutePlannerController.php:2341
    note: actionSketchFinalize — server guard
  - path: backend/views/route-planner/sketch-planner.php
    note: confirm-overlay modal pattern to extend
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - route-planner
  - release-blocker
  - ui
attention: null
version: 2
---

## Problem
Austin's top release worry: someone overwriting a good live plan — especially ON the delivery day, when vehicles may already be loading or out. Finalising today's date replaces the live run plan; today it's one click.

## Fix
- Client: when the sketch's plan date == today, the finalise flow shows a distinct red warning modal — "You are replacing TODAY'S live plan; vehicles may already be loading or on the road" — requiring the user to TYPE the word TODAY to enable the confirm button. Normal (future-date) finalise flow unchanged.
- Server: sketch-finalize rejects a same-day finalise unless the request carries confirm_today=1 — the guard holds even if the JS is bypassed/stale.
- Log the same-day finalise (who, when, sketch id) via SketchPlanLogger for the audit trail.