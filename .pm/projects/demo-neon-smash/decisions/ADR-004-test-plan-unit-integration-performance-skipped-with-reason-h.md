---
id: ADR-004
slug: test-plan-unit-integration-performance-skipped-with-reason-h
title: "Test plan: unit + integration, performance skipped with reason, human UAT"
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Sam Patel
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
kind: test-plan
---

> Drafter: Claude (with Sam Patel) · Accepted by: Austin · Decided at the scoping & design review (M-005), 2026-06-04.

## Context
What "tested" means for Neon Smash, per test type, with explicit skip-with-reason for the types that don't apply. Tests are written alongside the code and count toward a ticket's "done".

## Scope by test type

### Unit (REQUIRED) — game rules
- Ball↔wall, ball↔paddle, ball↔brick reflection (angles + corner cases).
- Scoring: base points, combo multiplier increments, and reset-on-miss.
- Lives: decrement on miss, game-over at zero, win when the last brick clears.
- Power-up effects apply and expire correctly.
- **Negative cases:** trapped ball / NaN velocity, simultaneous multi-brick hit, paddle pinned at a screen edge.

### Integration (REQUIRED) — leaderboard service
- One real end-to-end pass per endpoint against a temporary SQLite file.
- Every failure-mode row in the leaderboard API ADR has at least one test (expired token, replay→409, bad payload→400, ceiling→422, write error→503).
- Persistence: scores survive a service restart.

### Performance (SKIPPED — with reason)
- Single player, localhost, a handful of requests per game — no concurrency or load concern. Revisit only if this ever became multi-user (out of scope). The 60fps target is a client-render concern, verified by eye plus a dev frame-time counter, not a load test.

### UAT (REQUIRED, human) — play-through
- A human (Jordan + Austin) plays start → game-over → leaderboard on desktop and at phone width before M4 is called done.
- Confirms "feel": the juice lands, controls are responsive, nothing janky. Claude cannot self-UAT a game's feel.

## Exit criteria
- Unit + integration green (`bun test`).
- Every leaderboard-API failure row covered.
- One human play-through signed off, no critical/high defects open.

## Test data
- Synthetic score sets (empty board, full top-10, tie scores, max-int score) committed to the repo.