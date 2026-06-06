---
id: ADR-007
slug: change-from-plan-hosted-not-local-only-node-bun-json-store-n
title: "Change from plan: hosted (not local-only) + Node/Bun + JSON store (no Vite/SQLite)"
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Claude
project: demo-neon-smash
supersedes: ADR-001
superseded_by: null
tickets: []
kind: change-control
---

> Drafter: Claude · Accepted by: Austin · 2026-06-06. Supersedes the relevant parts of ADR-001 and amends the charter scope-out.

## Context
Two things changed between the plan and the build. Both are recorded here rather than absorbed silently.

## Change 1 — Hosting (scope change)
The charter's **scope-out** said *"No production or cloud hosting — local run only."* We chose to deploy to the pm-tool server on a public domain to demo the deploy flow. That scope-out line is **superseded** by this ADR; the how is in the deployment ADR.

## Change 2 — Stack (supersedes ADR-001)
ADR-001 specified **Bun + Vite + SQLite**. Actual build:
- **No Vite / no bundler** — the client is vanilla ES-module JavaScript + Canvas, served as static files. Reason: zero build step → "it just runs," and it avoids needing a toolchain on a machine without Bun.
- **JSON file store, not SQLite** — `server/data/scores.json` meets every leaderboard acceptance criterion (including persists-across-restart) with no database dependency.
- **Runs on plain Node locally and Bun on the server** — the server uses only Node-standard APIs, so both run it unchanged.

## Time impact (hours)
Net **time saved** — roughly a couple of hours of toolchain/DB setup avoided. No schedule slip.

## Consequences
- The leaderboard "service" is simpler than ADR-001 imagined; the ADR-003 API contract + failure modes are unchanged and fully met.
- If this ever became multi-user, revisit SQLite (the JSON store is single-writer). Out of scope now.
- ADR-001 stays as the original rationale; this ADR is the current truth.