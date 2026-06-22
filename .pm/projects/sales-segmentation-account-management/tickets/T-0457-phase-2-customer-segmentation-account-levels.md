---
id: T-0457
title: Phase 2 · Customer segmentation / account levels
type: feature
state: triaged
created: 2026-06-22T21:41:39Z
updated: 2026-06-22T21:41:39Z
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
version: 1
---

## What this is

Give every customer a clear **account level** (system / incubation / account-managed / strategic) and segment, so routing, ownership and attention all have something to act on. It builds on the customer **score** (the scoring deliverable) plus the customer's value. This is the "definitions table" the scoping workshop named as the unblocker for the whole phase.

## Where we are

Scores exist for ~530 customers, and the system already has customer tiers. What's missing is the agreed **level definitions** and the **thresholds** that turn a score + value into a level.

## The plan

- **Agree the account-level definitions** and the value/score **thresholds** that map score + value → level (a short, data-informed session against the live scores).
- **Apply** the level + segment to each customer; add a **confirm/override tick-box** on the quote/customer screens; **log corrections** (those corrections also improve future routing).
- Show **actual value** (human-entered) vs **potential value** (system-suggested, human-overridable).
- Stand up the **review loop** — who checks segmentation accuracy, and how often.

## Open / still to decide (the gating dependencies)

- The value/score **thresholds** — not decided yet.
- The **qualification** approach (qualified vs not, industry-specific questions) — needs further meetings.
- The **review owner + cadence**.