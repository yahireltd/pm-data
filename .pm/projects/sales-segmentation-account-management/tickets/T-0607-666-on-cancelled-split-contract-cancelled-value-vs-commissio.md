---
id: T-0607
title: £666 on cancelled split contract — cancelled value vs commission paid on team targets
type: bug
state: triaged
created: 2026-07-17T06:34:34Z
updated: 2026-07-17T07:24:22Z
project: sales-segmentation-account-management
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
  channel: meeting M-010
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - Root cause identified for the £666 showing on Paula's Q2 despite the split contract being cancelled to £0.
  - Cancelled contracts no longer contribute their value to team target / commission thresholds (per the agreed rule).
  - A clear rule is documented for cancelled commission on targets, covering paid vs invoiced.
out_of_scope: []
code_anchors: []
relates:
  - T-0470
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
milestone: MS-001
defect_status: confirmed
---

## Problem
A split contract that was **cancelled (value £0)** is still showing **£666** on Paula's Q2 results. Commission thresholds appear to be counting value that should have been removed on cancellation.

## Context
Raised by Nathan at **M-010** (Sales Phase 1 prep). Broader concern flagged in the same meeting: **cancelled commission on team targets** needs review and clear rules/logic, including **paid and invoiced** values. Owner for investigation: **Zsolt**.

## Investigation (Zsolt)
- Look into cancelled value vs commission paid: why a £0-cancelled split contract still contributes £666 to the Q2 target.
- Determine the correct rule for cancelled commission on targets (paid vs invoiced).
