---
id: T-0283
title: "Leaderboard service: Bun + SQLite API"
type: feature
state: ready
created: 2026-06-06T01:10:22Z
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
  - GET /api/token issues a single-use, time-limited token.
  - GET /api/scores returns the top-10, sorted high to low.
  - POST /api/score validates initials (3× A–Z), score (non-negative integer), a single-use token and signature, and returns the new rank or a typed error.
  - Scores persist in SQLite across a service restart.
  - Every failure mode in ADR-003 returns its specified status (401/409/400/422/503) and has an integration test.
out_of_scope:
  - Accounts or auth beyond the per-submit token (scope-out).
  - Hardened anti-cheat — forging is accepted risk (ADR-002).
code_anchors:
  - path: server/src/index.ts
    symbol: startServer
  - path: server/src/routes/scores.ts
    symbol: scoreRoutes
  - path: server/src/db.ts
    symbol: Db
  - path: server/src/scores.test.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-2
  - backend
  - leaderboard
  - integration
attention: null
backlog_status: confirmed_for_release
estimated_effort: L
source: charter
---

## Problem
The 'global' in 'global leaderboard' is this service. It must accept honest scores, reject obvious junk, and survive restarts — implementing the contract we already agreed.

## Context
- Part of milestone M3 · Online leaderboard (MS-003).
- Implements the server half of ADR-003 (the API contract + failure modes) and the score-integrity decision in ADR-002.

## Design notes
- Single-use token store (in-memory map or SQLite table) with a 10-minute expiry; burn on use → replay returns 409.
- Verify HMAC over `score+token`; reject scores above a sane ceiling (422); validate initials/score shape (400).
- Integration tests (ADR-004) cover one happy path per endpoint AND every failure row in ADR-003.