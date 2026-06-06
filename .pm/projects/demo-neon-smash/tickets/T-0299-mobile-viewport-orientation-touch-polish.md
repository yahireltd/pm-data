---
id: T-0299
title: Mobile viewport, orientation & touch polish
type: feature
state: ready
created: 2026-06-06T03:34:51Z
updated: 2026-06-06T03:36:04Z
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
  - Orientation is locked to portrait on both platforms.
  - Safe-area insets (notch, home indicator) are respected — nothing is clipped.
  - Pinch-zoom, double-tap-zoom, and overscroll/rubber-banding are disabled.
  - Dragging to move the paddle feels responsive on a real phone.
out_of_scope:
  - Landscape support (backlog).
  - Tablet-specific layouts.
code_anchors:
  - path: capacitor.config.ts
  - path: client/index.html
    lines: 1-12
  - path: client/src/styles.css
  - path: client/src/main.js
    symbol: pointerToCanvasX
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprint-3
  - mobile
  - ux
attention: null
backlog_status: in_review
estimated_effort: S
source: discovered
---

## Problem
A wrapped web game feels wrong without orientation lock, safe-area handling, and zoom/scroll suppression.

## Design notes
`viewport-fit=cover` + CSS `env(safe-area-inset-*)`; lock orientation via Capacitor config; disable touch-zoom and overscroll.