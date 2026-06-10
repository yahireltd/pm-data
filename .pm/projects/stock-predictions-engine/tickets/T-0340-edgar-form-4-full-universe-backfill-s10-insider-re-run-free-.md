---
id: T-0340
title: EDGAR Form-4 full-universe backfill + S10 insider re-run (free, pre-registered)
type: feature
state: triaged
created: 2026-06-10T01:51:06Z
updated: 2026-06-10T01:51:06Z
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
  - Form-4 panel covers the PIT universe 2016+ with filing dates
  - S10 re-run against unchanged registration, audited verdict either way
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
  - data
attention: null
version: 1
---

## Problem
S10 insider-cluster failed on data, not concept: data/insider/ covers 19 mega-caps with exactly 2 cluster events (need ≥300). Cluster buying is a small/mid-cap phenomenon; SEC EDGAR Form 4 is free and PIT (filing timestamps).

## Action
Backfill Form 4 across the full PIT universe back to ~2016 from EDGAR, then re-run scripts/train_s10_insider.py with the ORIGINAL registration unchanged (n≥300 clusters, same Sharpe/null bars — no loosening to manufacture a pass).