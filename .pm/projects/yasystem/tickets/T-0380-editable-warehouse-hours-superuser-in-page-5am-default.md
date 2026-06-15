---
id: T-0380
title: Editable warehouse hours (superuser, in-page) + 5AM default
type: feature
state: triaged
created: 2026-06-15T16:15:02Z
updated: 2026-06-15T16:15:02Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Default warehouse open hour is 05:00
  - Warehouse hours editable in-page by superusers only (non-superusers don't see/can't use the editor; server enforces can('SuperUsers'))
  - Saved hours persist and are used by the visualiser shading AND the turnaround eyes everywhere
  - Falls back to params/05:00-22:00 when the table/row is absent
out_of_scope: []
code_anchors:
  - path: common/models/WarehouseSettings.php
  - path: console/migrations/m260615_140000_create_warehouse_settings.php
  - path: backend/controllers/RoutePlannerController.php
    symbol: actionTurnaroundSaveHours
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - turnaround-visualiser
attention: null
version: 1
---

## Problem

Warehouse hours were params-only (deploy to change) and defaulted to 06:00. Ops wants the default to be 05:00 and a way to adjust the window from the visualiser page, restricted to superusers.

## Work

- New `warehouse_settings` table (single row) + `WarehouseSettings` model with cached `getHours()` / `setHours()`. Falls back to params then 05:00–22:00 and tolerates the table being absent (stale DB copy).
- `YaRuns::warehouseHours()` now delegates to `WarehouseSettings::getHours()` — so the turnaround eyes everywhere pick up the live value.
- Default open hour changed to 05:00 (params + seed + fallback).
- Superuser editor in the visualiser toolbar (shown when `warehouse.can_edit`), posting to `actionTurnaroundSaveHours` (gated on `Yii::$app->user->can('SuperUsers')`), which validates and saves; the page reloads so shading/bands recompute.

## Notes

Migration m260615_140000_create_warehouse_settings must run on each environment.