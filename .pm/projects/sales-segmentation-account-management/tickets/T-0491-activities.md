---
id: T-0491
title: Activities
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:21:23Z
updated: 2026-06-30T16:11:11Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 12288
reporter: null
assignee: null
acceptance_criteria:
  - Define the activity types + cadence per Stewardship Level (calls, meetings, plan reviews, follow-ups, site/venue capture) recorded against the customer.
  - Reuse the existing Activities/review record (type 15 / getLastReview) rather than a new system where possible.
  - Activities feed the Conformance score (T-0492 — done on cadence?) and the conversion checklist / 'in the bag' % (T-0497).
  - Each activity can carry/trigger a script (T-0490); uses agreed terminology.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: reuse Activities review record (type 15, getLastReview :424)
relates:
  - T-0490
  - T-0492
  - T-0497
  - T-0496
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## Problem
Define the activities (and cadence) that make up each level's playbook and that we record to prove the process ran.

## Design notes
Per-level activity set + cadence; reuse the existing Activities/review record (type 15) where possible. Activities are
the unit that **Conformance** (T-0492) checks ("done on cadence?") and that drive the **conversion checklist** (T-0497).
Each can trigger a script (T-0490).

## Relates
T-0490, T-0492, T-0497, T-0496.