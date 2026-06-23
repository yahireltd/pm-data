---
id: T-0457
title: Phase 2 · Account levels & assignment (system / incubation / account / strategic)
type: feature
state: triaged
created: 2026-06-22T21:41:39Z
updated: 2026-06-23T14:11:19Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-002
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Every customer carries an account level (system / incubation / account-managed / strategic) + segment, derived from agreed score+value thresholds.
  - A confirm/override tick-box lets a human correct the system's suggested level; corrections are logged.
  - Actual value (human-entered) and potential value (system-suggested, overridable) are shown per customer.
  - The segmentation review loop has a named owner + cadence and runs.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 2
---

## What this is

The **account-level / assignment** layer (the "C" in [[ADR-001]]): give every customer a level — system / incubation / account-managed / strategic — and route it to a conversion process. It **consumes** the segment (T-0473) + the score (T-0456); it is not the segmentation itself (which moved to MS-011 / T-0473 after we untangled the three layers).

## The plan

- Agree the **level definitions** + the **score+value thresholds** that map (segment + score + value) → level.
- Apply level to each customer; **confirm/override tick-box** (doubles as the segmentation review-queue's human side); **log corrections**.
- Show **actual value** (from contracts) vs **potential value** (system-suggested, overridable).
- Stand up the **review loop** (owner + cadence) — the quarterly transfer-window reassignment.

## Open / to decide
- The value/score thresholds. The qualification approach. Review owner + cadence. (These are the workshop's to settle.)

## Links
Consumes T-0456 (score) + T-0473 (segment vocab); pairs with T-0474 (scrape build).