---
id: T-0444
title: movement_eval harness + god-mode-OFF survival scorecard
type: feature
state: triaged
created: 2026-06-19T04:48:41Z
updated: 2026-06-19T04:48:41Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude
assignee: null
acceptance_criteria:
  - "Produces a reproducible per-run scorecard: level progress, survival with GOD MODE OFF (deaths, time-alive), stuck-event count, door-open success."
  - Reports a human-likeness measure vs a held-out human session.
  - Runs headless on Xash and renders a visual artifact (contact sheet / GIF) per run.
  - Scorecard is the acceptance instrument for MS-011 and feeds MS-007.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - movement
  - MS-011
  - ADR-009
  - MS-007
attention: null
version: 1
---

## Problem
We need an honest, reproducible way to measure whether the movement policy is actually a *good AI player* — for a game-dev buyer, that means believable behavior and progress, NOT aim mAP.

## Context
Extends MS-007's eval-harness/scorecard thinking. The adversarial review stressed: BC from god-mode play teaches no-cover behavior, so the headline survival number MUST be measured with **god mode OFF**. A game-dev buyer cares about plausibility (uses cover, opens doors, doesn't get stuck), not kill-rate.

## Approach
- Build `movement_eval` (mirrors harness.py) producing a per-run scorecard:
  - Level progress / checkpoints reached + trigger-to-trigger time.
  - **Survival with GOD MODE OFF**: deaths/level, time-alive.
  - Stuck events (no-progress windows) — target near-zero.
  - Door-open success rate.
  - Human-likeness vs a held-out human session (action-distribution distance / qualitative review).
- Reproducible (seeded map start, fixed route) and rendered (contact-sheet/GIF) like the existing harness.