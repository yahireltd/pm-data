---
id: ADR-007
slug: held-out-test-methodology-and-the-leaky-split-trust-gate
title: Held-out TEST methodology and the leaky-split trust gate
state: accepted
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
train.py today has only train/val (val both early-stops AND is reported) and silently falls back to a leaky random split on low-diversity data — the exact mechanism that produced the inflated HL mAP@50 0.986. The fix must be a recorded decision, not just a code change.

## Decision
train.py emits train / val / TEST. TEST is never used for training, early-stopping, or model selection — it is touched once for the headline. Grouping is upgraded from pHash-only to scene/session id so two angles of the same room stay on one side. Splits are deterministic and seed-recorded. A degenerate (low-diversity) split may NOT silently produce a trustworthy number: it is marked UNTRUSTWORTHY in the manifest or requires an explicit `--allow-leaky-split`. Reproducibility is scoped to dataset+split bit-identity; model bit-reproducibility is NOT claimed (CUDA nondeterminism) — model stability is metric-within-band.

## Consequences
Codifies and extends the train.py degenerate-split discipline across all eval layers (MS-005/007). Trust status becomes a recorded field consumed by the scorecard ship gate.