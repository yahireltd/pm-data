---
id: T-0610
title: Activity-editing simplification — Call/Email/Meeting/Task + icon (process-safe capture)
type: spike
state: triaged
created: 2026-07-17T07:59:38Z
updated: 2026-07-17T08:01:29Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
  channel: meeting M-010
assignee: null
acceptance_criteria:
  - "A recommended design is produced for a simpler activity-capture model: top-level Call/Email/Meeting/Task + exact-activity selection, with an icon per type."
  - The approach removes / constrains free editing of activities after the fact (protects process management).
  - "An explicit decision is recorded: implement now vs defer as a deep-dive, with a rough effort estimate."
out_of_scope: []
code_anchors: []
relates:
  - T-0468
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
Activities can currently be **edited freely**, but that shouldn't be allowed — it affects process management and we can't have activities adjusted after the fact. We need a simpler, quicker capture model that doesn't rely on free editing.

## Idea (from M-010, Nathan)
- Pick a top-level type: **Call / Email / Meeting / Task**, then select the exact activity underneath.
- The type is denoted by an **icon** (call/email/meeting shown with an icon at the top level).
- Flagged as a possible **deep-dive** — may warrant a proper design pass rather than a quick change.

## To decide
- Whether this is a quick change or a larger rework.
- The Manage Activities page (`/sales/manage-activities`) is the likely surface.
