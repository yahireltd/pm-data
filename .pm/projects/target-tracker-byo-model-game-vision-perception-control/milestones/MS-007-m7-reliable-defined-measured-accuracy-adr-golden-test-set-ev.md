---
id: MS-007
slug: m7-reliable-defined-measured-accuracy-adr-golden-test-set-ev
title: "M7 — 'Reliable' defined & measured: accuracy ADR, golden TEST set, eval harness, scorecards"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 7168
created: 2026-06-19T00:28:51Z
updated: 2026-06-19T00:28:51Z
acceptance_criteria:
  - An accepted ADR fixes numeric reliability thresholds for base + specialist (mAP@50, mAP@50-95, precision at deploy conf, per-slice recall floors on {far <40px, >=50% occluded, motion-blurred}), the scene-disjoint held-out protocol, min held-out size (>=300 instances / >=20 scenes) AND a per-slice minimum-N so each slice recall is statistically measurable; ratified after the first honest run
  - A frozen, HUMAN-verified, scene-disjoint HL golden TEST set (halflife_test_v1) exists — multiple NPC types/distances/maps/occlusion, every label human-confirmed (not auto-labeled), slice-tagged, version-pinned with per-frame verifier+timestamp
  - "eval_detect.py scores any model on the frozen TEST set: mAP@50/mAP@50-95/P/R with 95% bootstrap CIs, per-slice mAP/recall, PR + confidence-vs-F1 curves, error contact sheet — and MUST score the deployed ONNX path; a dedicated ONNX-vs-.pt parity gate asserts the deployed boxes match ultralytics within tolerance"
  - Auto-label precision/recall/IoU audited vs human ground truth (capture + qualify paths); --agree-iou chosen from a measured precision/recall/yield curve; a calibration report (reliability diagram + ECE) per model; per-game deploy-vs-labeling thresholds derived from the held-out curve REPLACE the single hardcoded conf=0.35
  - A model registry maps each model to its data version + split seed/hashes + full metric set + eval-harness rev (incumbent artifacts retained re-runnably); a one-command per-game scorecard renders proxy mAP next to ground-truth hit-rate with a machine-checked PASS/FAIL; a regression gate blocks any candidate regressing a gated metric/slice; honest HL + Godot reference scorecards exist (thin-data = NOT-shippable)
slip_records: []
phase: test
version: 1
---

# M7 — 'Reliable' defined & measured: accuracy ADR, golden TEST set, eval harness, scorecards

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
