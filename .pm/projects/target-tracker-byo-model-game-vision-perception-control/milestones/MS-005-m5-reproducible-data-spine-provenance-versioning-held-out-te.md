---
id: MS-005
slug: m5-reproducible-data-spine-provenance-versioning-held-out-te
title: "M5 — Reproducible data spine: provenance, versioning, held-out TEST split, QA gates"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 5120
created: 2026-06-19T00:28:29Z
updated: 2026-06-19T00:28:29Z
acceptance_criteria:
  - Every dataset carries a manifest (per-frame source game/map/session, origin type, labeling-model hashes+thresholds, qualify verdict, RNG seeds, pinned tool versions); identical inputs reproduce the same content hash; datasets.py lists/summarizes/diffs versions
  - train.py emits train/val/TEST lists (TEST never used for training, early-stop, or model selection); grouping upgradeable pHash->scene/session id; same version+seed reproduces byte-identical split lists (asserted by a test). Reproducibility scoped to dataset+split bit-identity, NOT model bit-identity (CUDA nondeterminism documented)
  - "The silent grouped->random degenerate fallback is replaced: a degenerate split records the metric UNTRUSTWORTHY in the manifest and refuses a trustworthy headline (or requires explicit --allow-leaky-split)"
  - qualify.py --max-per-cluster cap implemented (no-op today); emits coverage.json over named axes (game/map/NPC/distance/lighting) with a SUFFICIENT/INSUFFICIENT verdict naming under-covered axes
  - A dataset QA gate returns non-zero on injected defects (image/label mismatch, zero/out-of-range boxes, cross-split duplicate leakage, missing provenance) and blocks promotion; negatives + hard-negatives tracked as first-class with reported ratios
  - An adversarial regression suite proves each gate fires (planted cross-split near-dups stay same-side, off-domain art rejected, bad boxes caught, single-scene pool trips the degenerate gate) — retires the qualify/train 'unverified' gap
slip_records: []
phase: build
version: 1
---

# M5 — Reproducible data spine: provenance, versioning, held-out TEST split, QA gates

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
