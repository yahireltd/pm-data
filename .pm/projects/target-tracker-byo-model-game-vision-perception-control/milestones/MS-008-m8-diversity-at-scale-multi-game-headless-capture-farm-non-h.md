---
id: MS-008
slug: m8-diversity-at-scale-multi-game-headless-capture-farm-non-h
title: "M8 — Diversity at scale: multi-game headless capture farm + non-humanoid bootstrap"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 8192
created: 2026-06-19T00:28:59Z
updated: 2026-06-19T00:28:59Z
acceptance_criteria:
  - "A documented headless-onboarding playbook + launch template (the Xash bring-up generalized: build/launch, window placement at region, GPU-render verified, vsync/fps + raw-input fixes as adapter config) used to stand up >=2 more single-player/offline games beyond Godot+Xash, each GPU-rendered, capturable on :1, and guardrail-compliant"
  - A roaming/patrol agent (waypoints or look-around+advance with stuck-detection) moves through levels so capture.py sees varied NPCs/distances/lighting/backgrounds — fixing the 'one scientist, one spot' trap; a resumable orchestrator runs an unattended multi-game capture matrix (game x map x session) emitting per-frame provenance
  - A scored decision matrix selects a non-humanoid bootstrap strategy; qualify.py + capture.py support a pluggable proposer so --classes can be an arbitrary text prompt (e.g. 'robot enemy'), keeping the two-proposer ensemble-agreement structure (today both gates hard-depend on COCO 'person', emptying the trainset for non-humanoid games)
  - A second, non-humanoid game (or the Godot red-blob world as a synthetic non-person stand-in) is taken end-to-end capture->qualify->train->deploy to a specialist that PASSES the MS-007 reliability bar
  - A multi-game captured+qualified dataset exists whose coverage report passes SUFFICIENT and whose grouped train/val/test split is NON-degenerate (no silent leaky fallback) — retiring the top known gap; capture-variation mechanism (uinput look+fire vs passive/demo playback) decided via ADR
slip_records: []
phase: build
version: 1
---

# M8 — Diversity at scale: multi-game headless capture farm + non-humanoid bootstrap

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
