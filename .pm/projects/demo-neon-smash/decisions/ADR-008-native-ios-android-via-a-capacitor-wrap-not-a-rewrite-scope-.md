---
id: ADR-008
slug: native-ios-android-via-a-capacitor-wrap-not-a-rewrite-scope-
title: Native iOS + Android via a Capacitor wrap (not a rewrite) — scope expansion from web-only
state: proposed
decided: 2026-06-06
decided_by:
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
kind: dependency
---

> Drafter: Claude · **PROPOSED** — needs Austin's acceptance before the sprint goes in-progress (Claude-drafted ADRs require explicit human acceptance).

## Context
The game is a finished HTML5 Canvas web app, already live. We want it as native iOS + Android apps. The charter's scope-out said *"No native app — browser only"*, so this also **expands scope** (a charter amendment).

## Decision (proposed)
Wrap the existing web game with **Capacitor** — a native shell that runs the web app in a system WebView and exposes native plugins (haptics, status bar, splash). One codebase, both stores, ~100% of the game reused.

**Rejected alternatives:** a fully native rewrite (Swift + Kotlin — two codebases) or a cross-platform rewrite (React Native / Flutter) — both throw away a working game for no real benefit at a 2D-canvas game's scale.

## Sharp edges (dependency notes)
- Leaderboard calls become **cross-origin** from the app (the web build uses a relative `/api`). The app needs an absolute API base (the hosted server) AND the server needs **CORS** for the app origin / Capacitor scheme. (Ticketed.)
- Native "feel" is limited to WebView + plugins — fine for this game.
- Publishing needs paid Apple ($99/yr) + Google ($25) developer accounts — out of scope of the build sprint; we reach archive-ready + a documented checklist.

## Consequences
- Reuses the entire game + the live leaderboard.
- The charter scope-out "browser only" is amended by this ADR.