---
id: T-0339
title: PEAD v2 small-cap retest on the IPO-expanded panel (decides Benzinga keep/cancel)
type: spike
state: triaged
created: 2026-06-10T01:51:01Z
updated: 2026-06-10T01:51:01Z
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
  - Re-run with unchanged registration on the expanded panel, adversarially audited
  - Benzinga keep/cancel decision recorded as ADR amendment before next billing date
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
  - deadline
attention: null
version: 1
---

## Problem
PEAD v2 failed its null on the ~1,360-name large-cap-ish panel, but 121,677 of 240,823 earnings events were unpriceable for lack of bars — and the literature places PEAD in small/illiquid names. This is the one pre-registered escape hatch (ADR-011).

## Action
Re-run scripts/train_pead_v2.py unchanged (same registration, same null) on the merged IPO-expanded panel once the bars ticket lands. PASS → PEAD returns to the book and Benzinga stays. FAIL → PEAD permanently closed; cancel Benzinga Earnings before the next billing cycle (purchase date 2026-06-10).