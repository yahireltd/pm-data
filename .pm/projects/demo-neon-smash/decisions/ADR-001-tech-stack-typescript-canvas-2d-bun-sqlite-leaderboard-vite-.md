---
id: ADR-001
slug: tech-stack-typescript-canvas-2d-bun-sqlite-leaderboard-vite-
title: "Tech stack: TypeScript + Canvas 2D, Bun + SQLite leaderboard, Vite bundling"
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
kind: dependency
---

> Drafter: Claude · Accepted by: Austin · Decided at the scoping & design review (M-005), 2026-06-04.

## Context
Neon Smash is a single-screen arcade game plus a small leaderboard. It must run locally with one command, carry no heavy dependencies, be easy for an agent to build and a human to run. We are already a TypeScript/Bun shop (pm-tool itself is Bun + TS), so staying on that stack keeps the toolchain familiar and the demo honest.

## Decision
- **Language:** TypeScript throughout (game client and service).
- **Rendering:** HTML5 Canvas 2D. Breakout needs simple 2D shapes, at most a few hundred sprites, and a particle system — Canvas 2D is more than enough and has zero dependencies. We are NOT using WebGL / PixiJS / Three.
- **Leaderboard service:** Bun's built-in HTTP server + `bun:sqlite` for storage. No ORM, no external database.
- **Bundler / dev server:** Vite for the client (fast reloads, trivial production build).
- **Audio:** the Web Audio API directly, with a small set of short sound effects.

## Consequences
- One runtime (Bun) builds, serves, and stores — a single `bun run dev` is the whole story.
- We hand-roll a particle system and tweening — a few hundred lines, acceptable, and itself good demo material.
- SQLite is a single file on disk: trivial to reset, inspect, and back up during the demo.

## Sharp edges (dependency notes for the next agent)
- **Canvas is immediate-mode:** there is no retained scene graph — we redraw every frame. Keep per-frame allocations near zero (object pools for particles/balls) or the garbage collector will cause visible jank.
- **`bun:sqlite` is synchronous:** fine for a tiny leaderboard, but it is only ever touched inside the API handlers, never on a render path.
- **Vite dev vs build base paths differ:** asset URLs must be relative so the one-command production serve behaves the same as dev.
- **Canonical examples (planned):** game bootstrap at `client/src/main.ts`; service entry at `server/src/index.ts`.