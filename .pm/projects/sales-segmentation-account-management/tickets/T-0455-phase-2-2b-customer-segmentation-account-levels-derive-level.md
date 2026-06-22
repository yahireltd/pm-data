---
id: T-0455
title: Phase 2 · 2b — Customer segmentation / account levels (derive level from score + value)
type: feature
state: triaged
created: 2026-06-22T19:03:45Z
updated: 2026-06-22T19:03:45Z
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
  - Every customer carries an account level (system / incubation / account-managed / strategic) + segment, derived from the agreed score+value thresholds.
  - A confirm/override tick-box on the quote/customer screens lets a human correct the system's suggested level; corrections are logged.
  - Actual value (human-entered) and potential value (system-suggested, human-overridable) are shown per customer.
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

## Deliverable (2b of the scoring↔segmentation pair)

Give every customer a clear **account level** (system / incubation / account-managed / strategic) + segment, derived from the **score (2a)** + value, with a confirm/override loop. This is "the definitions table" the M-005 workshop named as the unblocker for the whole phase.

## Decision cycle

- **SEE:** the score (from **2a**) + actual value (human) / potential value (system-suggested, human-overridable) + history + industry + qualification answers.
- **DECIDE:** which account level + industry sub-type, and whether the customer is qualified.
- **ACT:** tag the level/segment; confirm/override tick-box on the quote/customer screens; log corrections (seeds ML routing).
- **OUTCOME:** every customer correctly levelled + owned → feeds routing + ownership.
- **REVIEW:** _decision needed — named reviewer + cadence._

## Decisions needed first (BLOCKERS — not buildable until settled)

1. **Value/score thresholds** — undecided. These turn the 2a score + value into a level. Needs a decision (likely a short, data-informed session against the live v3 scores).
2. **Qualification approach** — needs further meeting(s): qualified-vs-not, industry-specific questions, saved knowledge, re-qualification triggers (from M-005).
3. **Review owner + cadence** — undecided; a named reviewer is required (no loop without one).

## Links

**Blocked by 2a — Release & integrate the scoring.** First concrete artifact = the **definitions table** (per segment: what defines it, what data detects it, who confirms).