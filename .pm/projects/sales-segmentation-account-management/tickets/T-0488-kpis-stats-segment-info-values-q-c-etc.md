---
id: T-0488
title: KPI's / Stats / Segment Info Values £ Q C etc
type: feature
state: triaged
priority: p2
created: 2026-06-30T12:46:45Z
updated: 2026-06-30T16:09:51Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-012
children: []
order: 9216
reporter: null
assignee: null
acceptance_criteria:
  - "A defined metric set per Stewardship Level + Conversion Process: conversion/closing rate, repeat rate, realised £, potential £, share-of-wallet gained, value moved up/down levels per quarter, and leakage (lost opportunity)."
  - Segment-level rollups (£ / quote count / customer count by industry · company_type · level · tier) so we can see where value and effort concentrate.
  - Each metric is tied to a decision it drives (see/decide/act/outcome/review) — no vanity numbers.
  - "Metrics are computed at three review levels: whole company, the System steward (pool), and the individual owner."
  - Effectiveness metrics are reported against the conformant cohort (pairs with T-0492) and trend over Transfer Windows.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-terminology.md
    role: metric definitions (potential/realised/share-of-wallet)
  - path: common/components/PotentialEstimator.php
    role: potential £ source
  - path: common/components/RouteWBlender.php
    role: tier/score signals feeding metrics
relates:
  - T-0492
  - T-0493
  - T-0457
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## Problem
Define the KPIs / stats / segment values (£, quotes, customers) that tell us whether the operating model is working,
at company / steward / individual level.

## Design notes
Per-level + per-process metrics: conversion/closing rate, repeat rate, realised £, potential £, **share-of-wallet
gained**, level movement (up/down) per quarter, leakage. Segment rollups by industry/company_type/level/tier. Every
metric tied to the decision it drives (T-0322 method). Computed at three levels (company / steward / individual) and
reported on the **conformant cohort** (T-0492) so execution gaps don't distort effectiveness. Surfaced via T-0493
dashboards, reviewed at Transfer Windows.

## Relates
T-0492 (conformance gate), T-0493 (dashboards), T-0457 (level data), T-0488 feeds the quarterly review.