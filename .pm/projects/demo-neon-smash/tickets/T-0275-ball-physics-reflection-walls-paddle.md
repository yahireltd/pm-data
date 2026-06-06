---
id: T-0275
title: Ball physics & reflection (walls + paddle)
type: feature
state: ready
created: 2026-06-06T01:05:37Z
updated: 2026-06-06T01:06:47Z
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
  - The ball moves at constant speed and reflects off the side and top walls.
  - Bouncing off the paddle changes the ball's angle by where it lands (hitting the edges steers more).
  - The ball never tunnels through a wall or the paddle at high speed (swept or re-clamped collision).
  - Losing the ball off the bottom is detected and raised as a 'miss' event.
out_of_scope:
  - Ball–brick collision (next ticket).
  - Multi-ball (Sprint 2).
code_anchors:
  - path: client/src/game/ball.ts
    symbol: Ball
  - path: client/src/game/physics.ts
    symbol: reflect
  - path: client/src/game/physics.test.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-1
  - gameplay
  - physics
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
The ball is the heart of the game. Its bounce has to feel fair and controllable, and it must never escape the box through a wall at speed.

## Context
- Part of milestone M1 · Playable core loop (MS-001).
- Unit tests are required for the reflection maths, including negative cases (ADR-004).

## Design notes
- Paddle reflection: map hit position (-1..+1 across the paddle) to an exit angle, clamped so the ball never goes purely horizontal.
- Prevent tunnelling with swept collision or by clamping position back to the surface after an overshoot.
- Tests (ADR-004): wall/paddle/corner reflection angles, plus the NaN/zero-velocity and trapped-ball negative cases.