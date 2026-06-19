---
id: MS-001
slug: m1-gpu-display-xash3d-running
title: M1 — GPU display + Xash3D running
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 1024
created: 2026-06-14T01:12:33Z
updated: 2026-06-19T00:52:02Z
acceptance_criteria:
  - GPU-accelerated X display verified (glxinfo shows RTX 5070 Ti, not llvmpipe)
  - A real game frame is captured and pulled back to the dev machine
  - "Xash3D-FWGS (open-source engine) runs a single-player/offline game windowed at a fixed capture region on :1 — content is owned HL: Uplink (engine is free/open-source; game data is owned, NOT 'free content')"
  - "AMENDMENT 2026-06-19 (ADR-002, owner-approved): the original 'Xash3D runs FREE content...' criterion was reworded to distinguish the open-source engine from owned game data; MS-001 marked hit only when T-0370 lands the windowed-at-region launch + captured frame (done in MS-006)"
slip_records: []
version: 6
owner:
  kind: human
  name: austin@yahire.com
---

# M1 — GPU display + Xash3D running

## Acceptance criteria

## Notes
