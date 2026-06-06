---
id: ADR-002
slug: architecture-client-authoritative-game-with-a-thin-leaderboa
title: "Architecture: client-authoritative game with a thin leaderboard service"
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
---

> Drafter: Claude · Accepted by: Austin · Decided at the scoping & design review (M-005), 2026-06-04.

## Context
The game is single-player. The only server-side concern is a shared high-score table. We must decide how much authority the server has and how the two halves talk.

## Decision
The **browser is authoritative for gameplay** — all physics, scoring, and combo logic run client-side. The **server is a thin leaderboard service**: it issues a one-time play token, accepts a signed score submission, and serves the top-10. The client calls the service at only two moments (start: fetch token + top-10; end: submit score). The game is fully playable with the service down.

## Consequences
- Simple, offline-tolerant, no game state on the server.
- Score trust is weak by construction (see the security review below) — acceptable for a demo and explicitly out of scope to harden.

## Architecture review (hat: architect)
- **Fit:** two deployables (static client, Bun service) behind one dev command. No shared runtime state beyond SQLite.
- **Regression risk:** none — greenfield, no existing system touched.
- **New tech to acquire:** none beyond the chosen stack (the tech-stack ADR).
- **Temporary alternative considered:** a localStorage-only leaderboard (no server). Rejected — a *global* board is the whole point of the leaderboard milestone, and the service is what makes the integration design worth writing for this demo.

## Security review (hat: security)
- **Attack surface:** one POST (`/api/score`) and two GETs (`/api/token`, `/api/scores`). No auth, no accounts, no personal data.
- **Threat:** forged scores. **Mitigation:** the server issues a short-lived single-use nonce (token); the client signs the submission (HMAC over score+nonce); the server checks the signature, burns the nonce, and rejects scores above a sane ceiling or at an impossible rate.
- **Residual risk:** the signing secret ships in client code, so a determined attacker can still forge a valid submission. **Accepted** for a demo and recorded in scope-out. Real hardening (server-side replay, attestation) is explicitly out of scope.
- **Secrets / logging:** no secrets of value; logs carry initials + score only.

## Data privacy review (hat: data privacy)
- **Data collected:** three initials (free text) + a score + a timestamp. Initials are user-chosen and not tied to identity.
- **Classification:** NOT personal data — no identifier, no account, no IP stored. UK GDPR Article 4 not engaged; a DPIA is not triggered.
- **Retention:** keep all scores (demo); a reset is just deleting the SQLite file.
- **Note:** initials are free text, so the server must filter for length and profanity — content hygiene (a build ticket), not a privacy control.