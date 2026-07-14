---
id: ADR-001
slug: quality-grading-uses-4-labels-on-a-reserved-1-10-db-scale
title: Quality grading uses 4 labels on a reserved 1–10 DB scale
state: accepted
decided: 2026-07-14
decided_by:
  - Zsolt
  - Ben
project: stock-management-development
supersedes: null
superseded_by: null
tickets:
  - T-0553
version: 1
---

## Context
Quality grading needed a consistent, future-proof scale. Ben wanted the richness of a 1–10 scale; the practical steer ("simpler is better for now") favoured a small set of labels.

## Decision
Capture the grade with **4 labels — Good as new / Good / OK / Needs replaced** — but **store them on a reserved 1–10 numeric scale** in the DB (the labels map onto fixed points on the 1–10 range).

## Consequences
- We can make grading more granular later without losing or reinterpreting past inputs.
- The UI shows the label (not the raw number).
- Implemented in T-0553.