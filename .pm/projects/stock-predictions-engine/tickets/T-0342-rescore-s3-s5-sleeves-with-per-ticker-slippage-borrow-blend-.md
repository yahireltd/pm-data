---
id: T-0342
title: Rescore S3/S5 sleeves with per-ticker slippage + borrow (blend inputs still flat-5bps)
type: chore
state: triaged
created: 2026-06-10T01:51:32Z
updated: 2026-06-10T01:51:32Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - S5/S3 realistic CSVs produced
  - Blend + nulls re-run; number updated with provenance
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - research
  - ml
attention: null
version: 1
---

## Problem
The G2-passing ML blend (+0.370 realistic) uses realistic-cost S2 but flat-5bps S5/S3 — both are slow-turnover so the bias is modest, but unquantified. rescore_specialists_realistic_cost.py only covered the accepted v3b set (S1/S2).

## Action
Extend the rescore to S5 h120 and S3 h10 sleeve series; re-run run_meta_v4.py --returns-suffix _realistic and scripts/v4_nulls.py; update the blend's honest number.