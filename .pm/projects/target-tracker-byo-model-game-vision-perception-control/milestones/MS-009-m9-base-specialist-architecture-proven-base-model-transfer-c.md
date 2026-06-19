---
id: MS-009
slug: m9-base-specialist-architecture-proven-base-model-transfer-c
title: "M9 — Base + specialist architecture proven: base model, transfer curve, active-learning loop"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 9216
created: 2026-06-19T00:29:10Z
updated: 2026-06-19T00:29:10Z
acceptance_criteria:
  - A base/specialist dataset layout + class taxonomy is specified (base = generic 'character/enemy'; specialists may add game classes); a pooling tool assembles a base set from >=3 per-game qualified sets with provenance tags + an enforceable per-game cap; a first base model is trained on the versioned pool with a published model card; no redistributable base artifact contains scrape/unknown-license sources
  - train.py accepts --from-base <model-version> and records base dataset version + base model version + specialist dataset version — a reproducible lineage
  - "A base-vs-COCO transfer curve (held-out mAP vs # specialist frames) on a HELD-OUT GAME quantifies the data-efficiency head start against a PRE-SET 'material' bar (e.g. >=Nx fewer frames to the MS-007 bar, or >=X mAP at equal data) — the literal value proposition"
  - An active-learning loop demonstrably raises held-out TEST mAP across >=2 iterations against a FROZEN eval set, driven by calibrated-uncertainty / ensemble-disagreement sampling cross-referenced with under-represented coverage cells, with no leakage artifact
  - A results ledger shows held-out TEST mAP and in-game hit-rate side by side across >=2 dataset versions; a mAP-vs-hit-rate divergence is detectable and triggers a documented investigation path
slip_records: []
phase: build
version: 1
---

# M9 — Base + specialist architecture proven: base model, transfer curve, active-learning loop

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
