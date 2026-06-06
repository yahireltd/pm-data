---
id: T-0300
title: Native haptics, status bar & splash screen
type: feature
state: ready
created: 2026-06-06T03:34:56Z
updated: 2026-06-06T03:36:04Z
project: demo-neon-smash
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Haptic feedback fires on brick break, life lost, and game over.
  - The status bar is styled for the dark theme (light text, no clash).
  - A splash screen shows on launch and hides once the game is ready.
  - Falls back silently on devices without haptics.
out_of_scope:
  - Custom vibration patterns beyond light/medium/heavy.
code_anchors:
  - path: client/src/main.js
  - path: client/src/game.js
    symbol: Game
  - path: capacitor.config.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - juice
attention: null
backlog_status: in_review
estimated_effort: S
source: discovered
---

## Problem
Native-only feel: haptics on key events plus a proper status bar and splash.

## Design notes
Wrap `@capacitor/haptics` behind the existing audio/fx hook points so the web build is unaffected; configure `@capacitor/status-bar` + `@capacitor/splash-screen`.