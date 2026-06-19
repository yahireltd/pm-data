---
id: T-0435
title: "MS-004: LICENSE + RESPONSIBLE_USE, pin requirements-ml.txt, SERVER_SETUP runbook"
type: chore
state: triaged
created: 2026-06-19T00:32:15Z
updated: 2026-06-19T00:32:15Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - LICENSE + RESPONSIBLE_USE added (field-of-use noted as intent-only)
  - requirements-ml.txt pins exact ML dep versions matching ~/mlenv
  - SERVER_SETUP runbook reproduces the yolov8n 0->0.91 result on a clean box
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Scope
Add a LICENSE (owner picks; a field-of-use restriction reinforcing offline/no-evasion is an option — flag it as intent-only, not technically enforceable post-download) + a RESPONSIBLE_USE notice. Produce requirements-ml.txt pinning exact torch/ultralytics/open_clip_torch/onnxruntime-gpu/imagehash/evdev/opencv versions matching ~/mlenv (current requirements.txt is 4 lines, missing every ML dep). Write a SERVER_SETUP/runbook: nvidia-driver-580-open, display :1, Xash3D-FWGS native build + hlsdk-portable -8, runml.sh LD_LIBRARY_PATH, the LIVE_LOOP headless gotchas — so the stock-yolov8n-0 -> fine-tuned-0.91 result reproduces on a clean box.

Milestone: MS-004.