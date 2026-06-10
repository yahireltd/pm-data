---
id: T-0338
title: Merge daily_bars_ipo + _stale_patch into the main bar panel; one canonical refresh path
type: chore
state: triaged
created: 2026-06-10T01:50:57Z
updated: 2026-06-10T01:50:57Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - data/daily_bars/ max date == last trading day for >=95% of active names
  - RKLB, IONQ, ASTS, APP present with full histories
  - Single documented refresh command; partial-refresh root cause fixed
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
  - infra
attention: null
version: 1
---

## Problem
The 2026-06-10 refresh proved partial: 1,242/1,329 names stale after 2026-04-23 (MRVL famously ended 2026-03-31 at $99 while trading ~$300 live). The IPO-gap workflow is writing missing post-2016 listings to `data/daily_bars_ipo/` and stale tails to `_stale_patch/` (no-clobber while other jobs read the main panel).

## Action
When the IPO workflow completes: merge both dirs into `data/daily_bars/`, rebuild the PIT universe, fix whatever made `backfill_daily_bars.py` partial (ticker-list source vs full panel), and make the paper bot's coverage guard the canonical freshness check.