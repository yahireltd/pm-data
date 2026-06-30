---
id: T-0493
title: Dashboard Concepts
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:23:23Z
updated: 2026-06-30T16:10:11Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 14336
reporter: null
assignee: null
acceptance_criteria:
  - "Dashboard concepts for each review level: company (value/leakage/level movement), System-steward (pool closing & repeat rates), and individual owner (their book, conversion progress, conformance)."
  - Each dashboard is explicitly tied to the decision + action it drives and the review cadence (Transfer Window); if a screen doesn't change a decision it isn't built.
  - Includes the Value×Potential grid/scatter over the whole population, the Gold-Nugget (white-whale) queue, the big-fish/fast-track inbox, and the conversion-path worklist (what's outstanding).
  - Shows conformance alongside effectiveness so reviewers can separate 'process wrong' from 'not executed'.
  - Concept/mockup level (no build) — outputs feed the build tickets; reuses the read-only grid pattern.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: manager grid/scatter + queues (section 6.7)
  - path: backend/views/fast-track-lane/index.php
    role: existing read-only dashboard pattern
  - path: backend/views/segment-profile/index.php
    role: existing read-only grid pattern
relates:
  - T-0488
  - T-0492
  - T-0497
  - T-0457
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 4
---

## Problem
Decide what dashboards we need and what decision each one drives — for company, steward and individual reviews.

## Design notes
Concept/mockups (no build yet) for three audiences: **company** (value, leakage, level movement), **steward** (System
pool closing/repeat rates), **individual** (own book, conversion progress, conformance). Core views: the Value×Potential
grid/scatter, the **Gold-Nugget queue**, the big-fish/fast-track inbox, and the **conversion-path worklist** (T-0497).
Every dashboard names the decision + action it drives and its review cadence (Transfer Window). Show conformance next
to effectiveness (T-0492). Reuse the sales-scores / fast-track-lane / segment-profile read-only patterns.

## Relates
T-0488 (metrics), T-0492 (conformance), T-0497 (conversion worklist), T-0457 (data).