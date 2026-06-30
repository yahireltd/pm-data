---
id: T-0492
title: Process Conformance
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:22:27Z
updated: 2026-06-30T16:12:07Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 13312
reporter: null
assignee: null
acceptance_criteria:
  - "A per-account / per-owner CONFORMANCE score measures whether the process was actually executed: were the qualifying questions captured, the data-gate completed, the plan approved, and the follow-ups done on cadence."
  - Effectiveness metrics (T-0488) are reported ONLY on the conformant cohort, so a sound process is never written off because of incomplete execution — non-conformance is flagged as an EXECUTION issue, not a process issue.
  - A non-conformance worklist surfaces accounts where steps were skipped/overdue, by owner, for coaching at the Transfer Window.
  - The conformance definition (which gate steps + activities are 'mandatory') is explicit, versioned, and a workshop decision.
  - Distinguishes 'process is wrong' (poor results on the conformant cohort) from 'execution is the issue' (low conformance) in the review output.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-terminology.md
    role: Conformance vs Effectiveness definitions
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: ownership gates + transfer windows that conformance measures
relates:
  - T-0488
  - T-0493
  - T-0496
  - T-0457
  - T-0491
  - T-0494
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
We must be sure the process is being **followed and executed** before judging whether the process itself is right —
otherwise we'll bin a good process because of patchy execution (exactly what happened with Teresa).

## Design notes
Split two measurements (see terminology doc):
- **Conformance** — *was it executed?* Per account/owner: gate steps completed, qualifying questions captured, plan
  approved, follow-ups on cadence → a conformance score.
- **Effectiveness** — *did it work?* (T-0488 metrics) — computed **only on the conformant cohort**.
Output the two separately so reviews can tell "process wrong" (bad results despite conformance) from "execution issue"
(low conformance). A non-conformance worklist drives coaching at the **Transfer Window**. The 'mandatory steps'
definition is versioned + a workshop call.

## Relates
T-0488 (effectiveness metrics), T-0493 (dashboards), T-0496 (the gate steps), T-0457 (gate completion data).