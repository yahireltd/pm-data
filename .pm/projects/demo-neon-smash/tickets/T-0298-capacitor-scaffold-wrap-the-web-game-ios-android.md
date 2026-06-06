---
id: T-0298
title: Capacitor scaffold + wrap the web game (iOS + Android)
type: chore
state: ready
created: 2026-06-06T03:34:46Z
updated: 2026-06-06T03:36:03Z
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
  - Capacitor is added with both iOS and Android platforms configured.
  - The existing web game loads and is fully playable inside the native WebView on an iOS simulator and an Android emulator.
  - One documented command builds and syncs each platform.
  - No game code is rewritten — the wrap reuses client/ as-is.
out_of_scope:
  - Store submission (later ticket).
  - Any native rewrite of the game.
code_anchors:
  - path: capacitor.config.ts
  - path: package.json
  - path: client/index.html
  - path: ios/App/App
  - path: android/app/src/main
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - setup
attention: null
backlog_status: in_review
estimated_effort: M
source: discovered
---

## Problem
Get the live web game running inside a native iOS + Android shell with zero game rewrite (per the Capacitor-wrap decision).

## Design notes
Add Capacitor, point `webDir` at the client, add the ios + android platforms, `cap sync`. The game is static files, so the WebView loads `index.html` directly.