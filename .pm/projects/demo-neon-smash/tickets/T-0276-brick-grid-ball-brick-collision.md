---
id: T-0276
title: Brick grid & ball–brick collision
type: feature
state: ready
created: 2026-06-06T01:05:46Z
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
  - A configurable grid of bricks renders across the top of the play area.
  - The ball destroys a brick on contact and reflects correctly off the face it hit.
  - Simultaneous or corner hits resolve cleanly — the ball doesn't stick or double-count.
  - A 'brick destroyed' event fires (for scoring/juice later) and the remaining-brick count decrements.
out_of_scope:
  - Scoring and combos (Sprint 2).
  - Brick strength tiers / multi-hit bricks (product backlog).
code_anchors:
  - path: client/src/game/bricks.ts
    symbol: BrickGrid
  - path: client/src/game/physics.ts
    symbol: collideBrick
  - path: client/src/game/bricks.test.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-1
  - gameplay
attention: null
backlog_status: confirmed_for_release
estimated_effort: M
source: charter
---

## Problem
Clearing bricks is the objective. Collision has to be correct and cheap, since there can be dozens of bricks and the ball is fast.

## Context
- Part of milestone M1 · Playable core loop (MS-001).
- The 'brick destroyed' event is the hook Sprint 2 uses for combo scoring, particles and sound.

## Design notes
- Grid is data-driven (rows × cols, colours) so layouts are easy to change; spatial lookup by cell keeps collision O(1) per ball.
- Resolve one brick per tick per ball to avoid double-destroying on corner overlaps; pick the nearest face by penetration depth.
- Emit a typed `BrickDestroyed{ row, col, position }` event for downstream systems.