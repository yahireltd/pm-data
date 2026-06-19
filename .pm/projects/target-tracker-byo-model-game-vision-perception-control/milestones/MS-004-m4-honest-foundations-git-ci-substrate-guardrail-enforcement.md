---
id: MS-004
slug: m4-honest-foundations-git-ci-substrate-guardrail-enforcement
title: "M4 — Honest foundations: git/CI substrate, guardrail enforcement, ADRs, reproducible env"
project: target-tracker-byo-model-game-vision-perception-control
state: hit
order: 4096
created: 2026-06-19T00:28:21Z
updated: 2026-06-19T03:16:58Z
acceptance_criteria:
  - git repo initialized (.gitignore for venv/runs/__pycache__/datasets/weights) + branch/PR policy + a single test entrypoint (pytest/Make) and CI hook host — the substrate every later gate/manifest depends on (repo is NOT under version control today)
  - GUARDRAIL.md exists and is linked from README; every profiles/*.toml carries a required [scope] single_player_offline attestation; live.py + capture.py refuse profiles lacking it; a check_guardrail check fails CI on the evasion denylist or a missing [scope]
  - Zero anti-cheat-evasion framing remains in the repo outside GUARDRAIL/ADR text — control.py docstrings AND docs/LIVE_LOOP.md (the 'XTEST' note) audited and reframed to the legitimate captured-mode/XI2-raw rationale only
  - At least 5 ADRs recorded against P-0017 (guardrail/exclusions; reticle-vs-captured-uinput; Godot-proving-ground-vs-Xash; base+specialist architecture; T-0430 scope split)
  - LICENSE + RESPONSIBLE_USE added; requirements-ml.txt pins exact torch/ultralytics/open_clip/onnxruntime-gpu/imagehash/evdev/opencv versions; a SERVER_SETUP runbook reproduces the stock-yolov8n-0 -> fine-tuned-0.91 result on a clean box
  - RISK_REGISTER.md seeded with each named unknown (uinput-on-Xash, headless bring-up, generalization, leaky/low-diversity metrics, unverified qualify/train, data-provenance, guardrail drift), each with likelihood/impact/mitigation/trigger/owner
slip_records: []
phase: planning
version: 2
---

# M4 — Honest foundations: git/CI substrate, guardrail enforcement, ADRs, reproducible env

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
