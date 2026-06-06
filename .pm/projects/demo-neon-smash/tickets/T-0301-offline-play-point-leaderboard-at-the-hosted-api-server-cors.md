---
id: T-0301
title: Offline play + point leaderboard at the hosted API (+ server CORS)
type: feature
state: ready
created: 2026-06-06T03:35:01Z
updated: 2026-06-06T03:36:05Z
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
  - The game plays fully offline (assets bundled in the app).
  - The leaderboard client uses an absolute base URL (https://smashthegranny.yahire.com) on native, relative on web.
  - The server allows the app's origin via CORS (Capacitor scheme / app origin).
  - Offline submit shows the friendly fallback; an online submit from a device appears on the web board.
out_of_scope:
  - A separate mobile leaderboard — it's the same board as the web.
code_anchors:
  - path: client/src/leaderboard.js
    symbol: Leaderboard
  - path: capacitor.config.ts
  - path: server/src/index.js
    symbol: handleApi
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - leaderboard
  - integration
attention: null
backlog_status: in_review
estimated_effort: M
source: discovered
---

## Problem
The web build calls `/api` same-origin; the native app is a different origin, so calls fail without an absolute base + server CORS.

## Design notes
Detect native (Capacitor) → set the API base to the hosted server; add a CORS allow-origin for the `capacitor://` scheme on the server. Note: this one crosses into the **deployed** service, so it ships via `smashthegranny-deploy`.