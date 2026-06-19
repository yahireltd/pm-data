---
id: ADR-005
slug: the-reliability-bar-numeric-thresholds-scene-disjoint-held-o
title: The reliability bar — numeric thresholds, scene-disjoint held-out protocol, per-slice recall gate
state: proposed
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
Without a written, owner-ratified bar, "reliable accuracy" is unfalsifiable and no ship gate can exist. This is the keystone of the eval layer (MS-007).

## Decision (PROPOSED — ratify numbers after the first honest HL run)
A model is "reliable" if, on a HUMAN-labeled, scene-disjoint held-out TEST set of >=300 instances / >=20 scenes (with a per-slice minimum-N so each slice is statistically measurable): mAP@50 >= 0.85, mAP@50-95 >= 0.60, precision >= 0.90 at deploy conf, and recall >= 0.90 on EACH hard slice {far <40px, >=50% occluded, motion-blurred} — plus an in-game hit-rate floor within a bounded margin of the oracle-detector ceiling. Aggregate mAP is necessary-not-sufficient; per-slice recall is the gate.

## Consequences
Encoded as a machine-checked scorecard PASS/FAIL and a regression gate. Numbers ratified after seeing the first honest learning curve so they're reachable-but-not-trivial; the bar may differ per beachhead (accessibility vs QA tolerate different bars). Owner ratification required.