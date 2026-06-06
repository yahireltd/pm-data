---
id: T-0273
title: Fixed-timestep game loop & Canvas renderer
type: feature
state: ready
created: 2026-06-06T01:05:23Z
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
  - A fixed-timestep update loop runs game logic deterministically, decoupled from the render frame rate.
  - Rendering uses requestAnimationFrame and holds a steady 60fps with a full brick wall on screen.
  - The canvas scales crisply to its container and down to phone width (correct aspect, no blurring).
  - A simple scene switch exists (menu → playing → game-over), even if the menu/game-over screens are stubs.
out_of_scope:
  - Particles, screen shake and other juice (Sprint 2).
  - Final menu/game-over visuals — stubs are fine here.
code_anchors:
  - path: client/src/engine/loop.ts
    symbol: GameLoop
  - path: client/src/engine/renderer.ts
    symbol: Renderer
  - path: client/src/game/state.ts
    symbol: GameState
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-1
  - engine
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
Everything else (physics, scoring, juice) sits on top of a stable loop and renderer. Get the heartbeat right first: deterministic updates and a smooth 60fps draw.

## Context
- Rendering approach: Canvas 2D, immediate-mode (ADR-001). Architecture: client runs the whole game (ADR-002).
- Part of milestone M1 · Playable core loop (MS-001).

## Design notes
- Fixed timestep (e.g. 1/120s accumulator) for logic; interpolate on render so motion stays smooth if a frame is late.
- Object-pool anything created per frame (balls, later particles) — Canvas redraws every frame and GC churn = visible jank (TS-001 decision/action).
- Scene state machine lives in `game/state.ts`; later tickets add real screens.