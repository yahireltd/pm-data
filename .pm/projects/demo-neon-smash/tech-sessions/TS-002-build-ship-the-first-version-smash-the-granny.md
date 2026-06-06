---
id: TS-002
slug: build-ship-the-first-version-smash-the-granny
title: Build + ship the first version (Smash the Granny)
project: demo-neon-smash
created: 2026-06-06T03:27:27Z
updated: 2026-06-06T03:28:22Z
decisions:
  - Rebranded from "Neon Smash" to "Smash the Granny Out of It" — same brick-breaker, reframed as smashing the wall to free a trapped gran. Title, README, and on-screen copy updated.
  - Stack landed as a vanilla-JS + Canvas client and a Node/Bun server with a JSON score store — no Vite, no SQLite. Recorded as change-control ADR-007 (supersedes ADR-001); the ADR-003 API contract and failure modes are unchanged and fully met.
  - Shipped to the pm-tool server on its own domain + port (smashthegranny.yahire.com → :3002) behind the same Caddy, fully isolated from pm-tool. This is a scope change from the charter's "local only" — captured in ADR-007, with deployment + rollback steps in ADR-006. Live and verified (HTTPS 200, score submit works).
actions:
  - text: Add the read-only deploy key to the GitHub repo, then convert the server copy to a git checkout and add the smashthegranny-deploy script (git pull --ff-only + restart) to complete the pm-deploy-style push-to-deploy loop.
  - text: Write the unit + integration tests ADR-004 (test plan) calls for — the game rules and the leaderboard failure modes are currently verified by hand and by live integration checks, not yet committed automated tests.
  - text: Close T-0272 (scaffold + one-command run) — it's verified against the running build and sitting in review.
    ticket: T-0272
side_quests: []
problem: Turn the scoped plan into a working first version of the game, decide the real stack, and get it live — including the rebrand from "Neon Smash" to "Smash the Granny Out of It".
success_criterion: A playable first version is live on the internet via a one-command deploy that mirrors pm-tool, with the plan-vs-actual changes written down.
phase: ship
---

# TS-002: Build + ship the first version (Smash the Granny)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
