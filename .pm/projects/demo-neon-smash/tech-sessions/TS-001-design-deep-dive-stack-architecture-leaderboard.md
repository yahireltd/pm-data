---
id: TS-001
slug: design-deep-dive-stack-architecture-leaderboard
title: "Design deep-dive: stack, architecture & leaderboard"
project: demo-neon-smash
created: 2026-06-06T00:46:36Z
updated: 2026-06-06T01:03:20Z
decisions:
  - "Stack chosen: TypeScript + HTML5 Canvas 2D for the game, Bun + SQLite for the leaderboard, Vite to bundle. Picked for zero heavy dependencies and a one-command local run. (ADR-001)"
  - "Architecture: the browser is authoritative for all gameplay; the leaderboard is a thin service the game calls only at start and game-over; the game stays fully playable offline. (ADR-002)"
  - "Score integrity: submissions carry a single-use server token + signature, plus basic server-side sanity checks. A determined forger can still cheat — accepted as demo-level risk and recorded in scope-out. (ADR-002 / ADR-003)"
  - "Testing: unit-test the game rules, integration-test the leaderboard including every failure mode, skip load/performance testing with a reason, and require a human play-through before ship. (ADR-004)"
actions:
  - text: Object-pool particles and balls from the first frame to protect the 60fps target — Canvas redraws every frame, so garbage-collection churn shows up as visible jank.
  - text: Stand up the repo with a one-command run and an on-screen frame-time counter so the 60fps acceptance criterion is actually checkable.
side_quests:
  - "Idea raised mid-session: a daily seeded 'challenge' brick layout shared by all players. Parked to the product backlog — out of scope for v1."
problem: Before building, lock the load-bearing technical choices for Neon Smash — language and rendering, how the game and the leaderboard talk, how we keep scores honest enough, and what 'tested' means — so a fresh agent conversation can build without re-deriving them.
success_criterion: Each load-bearing decision is written down as an accepted decision record (ADR) with its reasoning, and the build tickets can point at those records instead of guessing.
phase: planning
meeting: M-005
---

# TS-001: Design deep-dive: stack, architecture & leaderboard

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
