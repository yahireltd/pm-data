---
id: T-0495
title: Data Capture / Validation
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:26:11Z
updated: 2026-06-30T16:11:37Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 16384
reporter: null
assignee: null
acceptance_criteria:
  - "The AI Score/Segment/suggested-Level is treated as a SUGGESTION: the UI lets a human ADJUST score & segment with a mandatory reason, and every override is logged to a correction log that can feed re-scoring ('computer learns')."
  - "A QUALIFY step captures the per-level qualifying answers + the missing firmographic data (the ~95% gap: companyType, decision-maker, budget, event calendar) at the point of taking ownership — enrichment-on-demand, not a bulk back-fill."
  - "Defines WHO validates and WHEN: steward (System) / exec (Incubation) / AM (Account) / senior AM (Strategic), at ownership hand-off; passing qualify advances Suggested → Qualified."
  - "Closes the loop with the T-0474 review-queue: 'proposed:<phrase>' no-fit segments and human corrections are reviewed and fed back."
  - Validation actions never silently overwrite — original AI suggestion is retained alongside the human-confirmed value for audit + model feedback.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-process-flow.mmd
    role: the VALIDATE → QUALIFY steps
  - path: common/models/CustomerSalesScores.php
    role: where score/segment + a correction/override lives
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: enrichment-on-demand at the Incubation gate (section 4)
relates:
  - T-0496
  - T-0497
  - T-0474
  - T-0457
  - T-0486
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
The score & segment are AI suggestions and ~95% of accounts lack firmographics. We need a way to (a) validate/correct
the suggestion, (b) capture the data we don't have, and (c) feed corrections back — with clear ownership of who does
it and when.

## Design notes
Ben's chain, made concrete: **AI suggests → Human adjusts → Human qualifies.**
- **Adjust** — inline confirm/override of score & segment with a reason; write to a correction log (retain the AI
  original). This both fixes the record and trains the next re-score.
- **Qualify** — at ownership hand-off, the owner answers the per-level qualifying questions (T-0496) and captures the
  missing data; passing advances **Qualified** and drives T-0497's 'in the bag' %.
- **Who/when** — steward / exec / AM / senior AM at hand-off, per level.
- Ties to T-0474's closed-vocab review queue (no-fit → 'proposed:<phrase>' → human review).

## Relates
T-0496 (questions/data per level), T-0497 (progress), T-0474 (vocab + review queue), T-0457 (engine).