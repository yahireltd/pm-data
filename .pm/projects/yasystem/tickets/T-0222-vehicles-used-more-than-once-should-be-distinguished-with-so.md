---
id: T-0222
title: vehicles used more than once should be distinguished with some icon / colour tone.
type: feature
state: done
priority: p2
created: 2026-06-04T14:04:22Z
updated: 2026-06-15T13:59:07Z
project: yasystem
section: null
parent: null
children: []
order: 19456
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Vehicles used more than once on the sketch planner should show some badge."
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1417
    model: claude
    started: 2026-06-04T14:17:25Z
    status: in_progress
    summary: Claimed via web UI
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
branch: PickingSketchSalesDashFriday
version: 7
---

## Problem

_Suggested feature from meeting M-003._

vehicles used more than once should be distinguished with some icon / colour tone.

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5`.

Amber `×N` badge on a route header when that route's `vehicle_id` is used on more than one route (uses the existing `vehicleUsageCount`).

**To test:**
1. Solve/build a plan where one vehicle runs **2+ routes** (multi-shift) → each of those route headers shows an amber `×N` badge.
2. Vehicles used only once show no badge.

---

**2026-06-15 13:59 — you**

done
