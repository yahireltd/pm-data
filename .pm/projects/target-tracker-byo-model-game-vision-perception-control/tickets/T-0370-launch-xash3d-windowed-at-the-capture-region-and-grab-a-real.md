---
id: T-0370
title: Launch Xash3D windowed at the capture region and grab a real frame back
type: chore
state: triaged
created: 2026-06-14T01:15:53Z
updated: 2026-06-14T01:15:53Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Game runs windowed at a known fixed region on the GPU DISPLAY
  - A real frame captured (ffmpeg x11grab or mss) clearly shows the game
  - The frame is pulled back to the dev machine and is viewable
out_of_scope:
  - Running a detection model on the frame (MS-002)
  - Closing the control loop (MS-003)
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ms-001
  - capture
attention: null
version: 1
---

## Problem
Prove the capture pipeline end-to-end: run the game windowed at a fixed region and pull a real frame back to the dev Mac — the input the ONNX detector (MS-002) will consume.

## Approach
1. Launch xash3d windowed at a known position/size (align with `tracker.py` MONITOR, or set MONITOR to the window).
2. Capture one frame via `ffmpeg -f x11grab` (or `mss`).
3. `scp` it back to the dev machine and view it.

## Conversation