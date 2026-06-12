---
id: T-0222
title: vehicles used more than once should be distinguished with some icon / colour tone.
type: feature
state: review
priority: p2
created: 2026-06-04T14:04:22Z
updated: 2026-06-12T00:11:57Z
project: logistics-route-planning-rollout
section: null
parent: null
children: []
order: 6144
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Vehicles used more than once on the sketch planner should show some badge.
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
labels: []
attention:
  needed_by: human
  reason: Implemented & committed (8b37aef5); ready for human review/testing — test steps in the Conversation.
  since: 2026-06-04T18:03:09Z
branch: SymbioticRoutePlanner
version: 1
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
