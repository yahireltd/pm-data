---
id: T-0281
title: "Power-ups: multi-ball & wider paddle"
type: feature
state: ready
created: 2026-06-06T01:10:06Z
updated: 2026-06-06T01:10:46Z
project: demo-neon-smash
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
  - Some bricks drop a power-up capsule the paddle can catch.
  - Multi-ball spawns two extra balls; losing one ball does NOT end the game while others are still live.
  - Wider-paddle temporarily widens the paddle and then reverts on a timer.
  - Active power-ups are indicated on the HUD and expire cleanly with no leftover state.
out_of_scope:
  - Other power-ups (laser, slow-mo, sticky paddle) — product backlog.
  - Drop-rate balancing beyond a simple percentage.
code_anchors:
  - path: client/src/game/powerups.ts
    symbol: PowerUpSystem
  - path: client/src/game/ball.ts
    symbol: Ball
  - path: client/src/ui/hud.ts
    symbol: Hud
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - gameplay
  - powerups
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
Two power-ups give the game variety and surprise without much surface area: multi-ball and wider paddle.

## Context
- Part of milestone M2 · Game feel & scoring (MS-002).
- Interacts with the lives rule from Sprint 1 (T-0277).

## Design notes
- IMPORTANT interaction: with multi-ball live, a life is only lost when the LAST ball leaves play — wire this through `Rules`, not the ball.
- Power-ups are timed effects in `PowerUpSystem`; capsules fall and are caught by paddle overlap.
- HUD shows active effects + remaining time.