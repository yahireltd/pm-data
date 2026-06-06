---
id: T-0279
title: Combo scoring system & score HUD
type: feature
state: ready
created: 2026-06-06T01:09:50Z
updated: 2026-06-06T01:10:45Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Each brick has a base point value and the HUD shows the live score.
  - Hitting bricks in a row without losing the ball builds a combo multiplier shown on screen.
  - Losing the ball past the paddle resets the combo to 1×.
  - The final score is exposed for the leaderboard ticket to read at game over.
out_of_scope:
  - Submitting the score online (leaderboard tickets).
  - Floating per-brick score popups (the juice ticket may add these).
code_anchors:
  - path: client/src/game/scoring.ts
    symbol: ScoreSystem
  - path: client/src/ui/hud.ts
    symbol: Hud
  - path: client/src/game/scoring.test.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - scoring
  - ui
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
A score with a combo multiplier is what makes an arcade game worth chasing — and it's what the leaderboard ranks. It hangs off the 'brick destroyed' event from Sprint 1.

## Context
- Part of milestone M2 · Game feel & scoring (MS-002).
- Consumes the `BrickDestroyed` event (T-0276); feeds the leaderboard (Sprint 2).

## Design notes
- Combo increments per brick while the ball is alive; a short window can keep it forgiving. Reset on miss.
- Multiplier curve kept gentle (e.g. +0.1 per hit, capped) so scores stay legible.
- Unit tests (ADR-004): base scoring, multiplier growth, and the reset-on-miss negative case.