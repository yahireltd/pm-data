---
id: T-0369
title: Install Xash3D (open GoldSrc) + free content on the server
type: chore
state: in_progress
created: 2026-06-14T01:15:48Z
updated: 2026-06-14T01:31:24Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - xash3d launches and reaches a menu/map on the GPU display
  - Only free/open content is used (no paid Half-Life assets)
  - The launch command is documented for the capture ticket
out_of_scope:
  - Online/multiplayer of any kind (excluded by project scope)
  - Detector / agent work (later milestones)
code_anchors:
  - path: README.md
  - path: live.py
    symbol: main
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260614-0131
    model: claude-opus-4-8
    started: 2026-06-14T01:31:24Z
    status: in_progress
labels:
  - ms-001
  - engine
  - xash3d
attention: null
version: 4
---

## Problem
We need an actual GoldSrc game to capture. Use **Xash3D** (open-source GoldSrc engine) + freely-licensed content — no Steam/paid Half-Life assets, per project scope.

## Approach
1. Install Xash3D (FleetView) — prebuilt binary or build from source (server has gcc/cmake-installable).
2. Provide a free/open content base (free GoldSrc content / aim map / free mod — no paid HL `valve` assets).
3. Verify it launches and reaches a menu/map on the GPU display from the previous ticket.

## Conversation