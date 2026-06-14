---
id: MS-002
slug: m2-pluggable-onnx-detector
title: M2 — Pluggable ONNX detector
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 2048
created: 2026-06-14T01:12:33Z
updated: 2026-06-14T01:12:33Z
acceptance_criteria:
  - Detector ABC + Detection dataclass defined
  - OnnxDetector runs a YOLO .onnx on the GPU and returns boxes
  - live.py uses the detector via config instead of hardcoded find_target
  - Detections visualized on captured frames
slip_records: []
version: 1
---

# M2 — Pluggable ONNX detector

## Acceptance criteria

## Notes
