---
id: MS-002
slug: m2-pluggable-onnx-detector
title: M2 — Pluggable ONNX detector
project: target-tracker-byo-model-game-vision-perception-control
state: hit
order: 2048
created: 2026-06-14T01:12:33Z
updated: 2026-06-18T22:51:41Z
acceptance_criteria:
  - Detector ABC + Detection dataclass defined
  - OnnxDetector runs a YOLO .onnx on the GPU and returns boxes
  - live.py uses the detector via config instead of hardcoded find_target
  - Detections visualized on captured frames
slip_records: []
version: 3
owner:
  kind: human
  name: ben@yahire.com
---

# M2 — Pluggable ONNX detector

## Acceptance criteria

## Notes
