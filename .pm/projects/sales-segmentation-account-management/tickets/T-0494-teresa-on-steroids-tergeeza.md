---
id: T-0494
title: Teresa On Steroids (Tergeeza)
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:24:37Z
updated: 2026-06-30T16:12:07Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 15360
reporter: null
assignee: null
acceptance_criteria:
  - "The upgraded System-only nurture + remarketing engine: cron/AI follow-up + don't-go / stopped-quote thresholds + remarketing → Yamembership, owned by the steward."
  - Runs OFF the live quote-save path (zero risk to the conversion path); uses the shared script library (T-0490).
  - Extend-or-replace decision taken on the T-0489 review data BEFORE build; scoped + estimated separately (it is not free and is the Phase-4 dependency the rest of the model leans on).
  - Measured by the System-pool KPIs (closing rate, repeat rate) and reported on the conformant cohort (T-0492).
out_of_scope: []
code_anchors:
  - path: common/models/TeresaFollowups.php
    role: current engine to extend/replace
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: Phase-4 nurture dependency (section 7, risk 4)
relates:
  - T-0489
  - T-0490
  - T-0492
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
System-only (the biggest bucket) and Lifetime follow-up are manual worklists until a real automated nurture engine
exists. This is that engine.

## Design notes
Cron/AI follow-up + don't-go/stopped-quote thresholds + remarketing → Yamembership, steward-owned, off the live quote
path, using the shared scripts (T-0490). Gated on the T-0489 review (extend vs replace). Separately scoped/estimated —
the honest Phase-4 dependency the operating model relies on. Success = System-pool KPIs on the conformant cohort.

## Relates
T-0489, T-0490, T-0492, T-0457.