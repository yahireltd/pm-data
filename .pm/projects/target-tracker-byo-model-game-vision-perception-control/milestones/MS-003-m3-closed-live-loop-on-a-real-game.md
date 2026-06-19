---
id: MS-003
slug: m3-closed-live-loop-on-a-real-game
title: M3 — Closed live loop on a real game
project: target-tracker-byo-model-game-vision-perception-control
state: hit
order: 3072
created: 2026-06-14T01:12:33Z
updated: 2026-06-19T03:15:31Z
acceptance_criteria:
  - Debug video shows the tracking working
  - Single-player/offline guardrail documented in README + config
  - "capture -> detect -> agent -> control runs against a real game: PROVEN end-to-end on Godot (visible-cursor reticle); the Xash3D captured-mode path is proven in MS-006"
  - "Aim converges onto a detected target: PROVEN via visible-cursor reticle on Godot; captured-mode relative-mouse (uinput) convergence on Xash is proven in MS-006 before MS-003 closes"
  - "AMENDMENT 2026-06-19 (ADR-002, owner-approved): original 'against Xash3D' + 'relative-mouse aim' criteria split into the proven Godot/reticle path and the open Xash/uinput path; MS-003 stays open until MS-006 evidence; MEMORY.md 'MS-003 CLOSED' note reconciled to the Godot/reticle proxy"
slip_records: []
version: 8
owner:
  kind: human
  name: Austin
---

# M3 — Closed live loop on a real game

## Acceptance criteria

## Notes
