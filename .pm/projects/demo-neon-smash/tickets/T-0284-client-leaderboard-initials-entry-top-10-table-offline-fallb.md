---
id: T-0284
title: "Client leaderboard: initials entry, top-10 table & offline fallback"
type: feature
state: ready
created: 2026-06-06T01:10:29Z
updated: 2026-06-06T01:10:47Z
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
  - At game over the player enters three initials and submits their score.
  - The top-10 table is shown and updates immediately after a successful submit, highlighting the player's row.
  - Every call has a 3-second timeout; if the service is unreachable the game still works and shows a friendly 'offline — score not saved' message.
  - Expired-token and duplicate-submit responses are handled gracefully (one retry, then give up) and never crash the game.
out_of_scope:
  - The server-side leaderboard logic (its own ticket).
  - Profanity filtering of initials beyond basic A–Z (the server owns content rules).
code_anchors:
  - path: client/src/net/leaderboard.ts
    symbol: LeaderboardClient
  - path: client/src/ui/gameover.ts
    symbol: GameOverScreen
  - path: client/src/ui/leaderboard.ts
    symbol: LeaderboardTable
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - leaderboard
  - integration
  - ui
attention: null
backlog_status: confirmed_for_release
estimated_effort: L
source: charter
---

## Problem
This is where a player's run becomes a score on the board — and where the game has to stay graceful when the network doesn't cooperate.

## Context
- Part of milestone M3 · Online leaderboard (MS-003).
- Implements the client half of ADR-003, especially the failure-modes table.

## Design notes
- `LeaderboardClient` wraps fetch with a 3s timeout and maps each ADR-003 status to a friendly outcome.
- The offline fallback is the load-bearing behaviour: the game must never block or crash because the board is down.
- Highlight the just-submitted row in the table; show the player's rank.