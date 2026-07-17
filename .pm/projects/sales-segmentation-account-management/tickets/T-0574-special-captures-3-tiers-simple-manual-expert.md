---
id: T-0574
title: Special captures — 3 tiers (Simple / Manual / Expert)
type: feature
state: triaged
created: 2026-07-15T07:21:30Z
updated: 2026-07-17T06:34:00Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: Nathan phase-1 prep doc
assignee: null
acceptance_criteria:
  - The 3 tiers (Simple/Manual/Expert) are defined, each with the criteria that place a capture in that tier.
  - Tier definitions are informed by Nathan's 16/7 doc, the customer-expectation doc, and Zac's spreadsheets.
  - Lift/large-item complexity is captured as a tiering factor.
  - The ML pricing-feedback idea is logged as a separate future spike (not built here).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sales
  - phase-1
  - special-captures
attention: null
version: 2
---

## Problem
Define the special-capture (bespoke/complex quote) tiers and how staff handle each.

## Proposal (M-010)
Three levels of special capture:
- **Simple** — automatable.
- **Manual** — for trained Jr staff.
- **Expert** — for trained experienced staff or manager sign-off.

## Inputs to consider before defining the tiers
- Nathan's document sent 16/7.
- The customer-expectation doc.
- Zac's spreadsheets.

## Ideas / factors
- **IDEA:** use ML on outcomes of pricing vs time vs actual, with feedback loops. (Note: new-ML model development is a current project scope-out — treat as a future spike, not part of this build.)
- What constitutes each tier: e.g. **lift availability** — large items vs lift size matters.
- Check the logistics + big-jobs page and that the feedback loops are working / better managed as part of the transformation.

## Follow-ups
- Training & process to be collaborated on; produce a facts/pointers doc.
- Confirm the inputting UX / software format.
