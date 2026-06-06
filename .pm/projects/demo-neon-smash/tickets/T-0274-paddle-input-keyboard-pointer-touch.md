---
id: T-0274
title: Paddle & input (keyboard + pointer/touch)
type: feature
state: ready
created: 2026-06-06T01:05:30Z
updated: 2026-06-06T01:06:47Z
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
  - The paddle moves left/right with the arrow keys and with A/D.
  - The paddle follows the pointer or touch X position when the player drags.
  - The paddle is clamped inside the play area and can't pass the side walls.
  - Movement feels responsive — no perceptible input lag at 60fps.
out_of_scope:
  - Gamepad support (product backlog).
  - Paddle-altering power-ups like wider paddle (Sprint 2).
code_anchors:
  - path: client/src/game/paddle.ts
    symbol: Paddle
  - path: client/src/engine/input.ts
    symbol: InputManager
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-1
  - gameplay
  - input
attention: null
backlog_status: confirmed_for_release
estimated_effort: S
source: charter
---

## Problem
The player needs a paddle that responds instantly on both desktop and touch, since the game must be playable down to phone width (scope-in).

## Context
- Part of milestone M1 · Playable core loop (MS-001).
- Touch support matters because "responsive down to phone width" is in scope.

## Design notes
- One `InputManager` normalises keyboard + pointer + touch into a single intended-paddle-X each tick; the paddle eases toward it to avoid jitter.
- Clamp against the same wall bounds the ball uses, so paddle and ball agree on the play area.