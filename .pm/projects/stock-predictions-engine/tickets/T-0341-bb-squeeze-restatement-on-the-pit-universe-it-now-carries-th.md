---
id: T-0341
title: BB_Squeeze restatement on the PIT universe (it now carries the whole book)
type: spike
state: triaged
created: 2026-06-10T01:51:27Z
updated: 2026-06-10T01:51:27Z
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
  - PIT-universe restated Sharpe with close-based exits
  - Null comparison reported
  - Adversarially audited; book sizing updated to the restated number
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
  - book
attention: null
version: 1
---

## Problem
BB_Squeeze (+1.726 @10bps) is the last sleeve standing and now carries the entire dry-run book — but its universe is a fixed ~96-name current-name large/mid list (survivorship-tinted) and its TP/SL fills assume touch=fill. After MR and PEAD both died under harder tests, the book's sole survivor deserves the same treatment BEFORE EXECUTE=1 sizing decisions.

## Action
Restate on the PIT top-ADV universe (same engine, monthly-rebuilt membership), close-based exits as the bot actually trades them (the live loop manages exits at close — measure the touch-vs-close divergence), and run a sensible null (e.g. random same-size entries with identical exit machinery, plus entry-signal permutation).