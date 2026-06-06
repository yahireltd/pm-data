---
id: ADR-003
slug: leaderboard-http-api-contract-and-failure-modes
title: Leaderboard HTTP API contract and failure modes
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
kind: integration
---

> Drafter: Claude · Accepted by: Austin · Decided at the scoping & design review (M-005), 2026-06-04.

## Context
The game client (browser) talks to the leaderboard service (Bun) over HTTP on localhost. This ADR fixes the contract so client and service can be built independently and a fresh agent conversation knows exactly how they interact.

## Decision — the contract
- **Transport:** JSON over HTTP, same origin in production (Vite proxy in dev). Three endpoints:
  - `GET /api/token` → `{ token, expires_at }` — a single-use nonce, valid 10 minutes.
  - `GET /api/scores?limit=10` → `{ scores: [{ initials, score, at }] }`, sorted high→low.
  - `POST /api/score` `{ initials, score, token, sig }` → `201 { rank }` or a typed error.
- **Schedule:** on demand — token + scores fetched at game start; score posted at game over. No polling.
- **Auth:** none for reads; writes carry the single-use `token` + `sig` (HMAC — see the architecture ADR's security review).
- **Data shape:** `initials` = exactly 3 chars `[A-Z]`; `score` = non-negative integer; the server clamps and validates both.
- **Idempotency:** the `token` is single-use; replaying a submission returns `409` and never double-inserts.

## Failure modes (mandatory)
| When… | Server does | Client does |
|---|---|---|
| Service unreachable at start | — | Skip the online board, show a local-only "offline" state, game still fully playable |
| Service unreachable at submit | — | Show "couldn't reach leaderboard — your score: N", offer one retry |
| Invalid / expired token | `401` | Fetch a fresh token, retry once, then give up gracefully |
| Replayed token | `409` | Treat as already-submitted; show the returned rank |
| Bad payload (initials/score) | `400` + reason | Friendly validation message; let the player fix their initials |
| Score above ceiling / impossible rate | `422`, logged | Generic "score rejected" — do not crash |
| SQLite write error | `503`, logged | Same path as "unreachable at submit" |

## Consequences
- The client treats the leaderboard as best-effort: every call has a 3-second timeout and a no-network fallback. This is the single most important behaviour and earns its own acceptance criterion on the leaderboard milestone (MS-003).
- Negative-path tests are mandatory (see the test-plan ADR): each failure row above gets at least one test.