---
id: T-0344
title: "Housekeeping: YHOO/AABA mapping, BBBY ticker-reuse guard, lapsed Benzinga news entitlement, options OI-field builder bug"
type: chore
state: triaged
created: 2026-06-10T01:51:46Z
updated: 2026-06-10T01:51:46Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - Each of the four items either fixed or explicitly parked with a note in the relevant loader/doc
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - data
  - hygiene
attention: null
version: 1
---

## Problem
Small data-integrity items found during the 2026-06-10 sweeps, none currently load-bearing:
1. Benzinga re-tickered Yahoo to AABA retroactively; pre-2015 Yahoo prints absent under either symbol — add a symbol-mapping note to the earnings loader.
2. BBBY-style ticker reuse (rows to 2026-04 under a reused symbol) — entity-mismatch guard needed when joining earnings to bars (PIT active flag mitigates, not eliminates).
3. Benzinga NEWS entitlement lapsed in the Polygon→Massive transition — historical news parquet can't be refreshed; decide revive ($99/mo) or formally park the news/sentiment pipeline (currently in no live sleeve).
4. src/pipeline/build_options_features.py:372 writes put_call_OI_ratio as a byte-copy of put_call_volume_ratio — fix or delete the column so nobody trusts it later (options are kill-listed but the panel remains on disk).