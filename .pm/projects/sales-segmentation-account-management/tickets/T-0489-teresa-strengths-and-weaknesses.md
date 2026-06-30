---
id: T-0489
title: Teresa Strengths and weaknesses
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:15:57Z
updated: 2026-06-30T16:11:51Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-012
children: []
order: 10240
reporter: null
assignee: null
acceptance_criteria:
  - "An evidence-based effectiveness review of the CURRENT Teresa follow-up automation (it runs but hasn't delivered): measure stopped-pool conversion and cost over a real window."
  - Produces an explicit extend-or-replace recommendation on data — not opinion.
  - Names what's missing today (the absence of stopped-pool reporting is why ineffectiveness went unnoticed for ~2 years).
  - Precedes and informs T-0494 (the upgrade).
out_of_scope: []
code_anchors:
  - path: common/models/TeresaFollowups.php
    role: current Teresa follow-up logic to evaluate
relates:
  - T-0494
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 2
---

## Problem
Teresa has run ~2 years with nobody checking it and it hasn't delivered — we need to know its real strengths/weaknesses
before building its successor.

## Design notes
Measure stopped-pool conversion + cost on real data; explicit extend-or-replace call. The missing stopped-pool
reporting is the root cause of years of unnoticed ineffectiveness. Feeds T-0494.

## Relates
T-0494.