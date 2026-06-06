---
id: T-0277
title: Lives, win/lose states & HUD
type: feature
state: ready
created: 2026-06-06T01:05:52Z
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
  - The player starts with 3 lives; a miss costs one; reaching zero shows a game-over state.
  - Clearing every brick shows a 'you win' state.
  - A HUD shows current lives and a score placeholder.
  - From game-over or win, the player can restart into a fresh game.
out_of_scope:
  - Submitting to the leaderboard (Sprint 2).
  - Sound effects (Sprint 2).
code_anchors:
  - path: client/src/game/rules.ts
    symbol: Rules
  - path: client/src/ui/hud.ts
    symbol: Hud
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
  - gameplay
  - ui
attention: null
backlog_status: confirmed_for_release
estimated_effort: S
source: charter
---

## Problem
A loop isn't a game until you can win or lose it. Wire the 'miss' and 'last brick cleared' events into lives, win/lose, and a restart.

## Context
- This ticket closes most of milestone M1 · Playable core loop (MS-001).
- Consumes the 'miss' event (ball ticket) and the 'brick destroyed' / brick-count (bricks ticket).

## Design notes
- `Rules` owns lives, win and lose; the HUD just renders what `Rules` exposes.
- Score is a placeholder here; Sprint 2's combo-scoring ticket fills it in.
- Restart fully resets ball, bricks, lives and score so repeated demo runs are clean.