---
id: T-0339
title: PEAD v2 small-cap retest on the IPO-expanded panel (decides Benzinga keep/cancel)
type: spike
state: triaged
created: 2026-06-10T01:51:01Z
updated: 2026-06-10T03:48:55Z
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
version: 2
---

## Problem
PEAD v2 failed its null on the ~1,360-name large-cap-ish panel, but 121,677 of 240,823 earnings events were unpriceable for lack of bars — and the literature places PEAD in small/illiquid names. This is the one pre-registered escape hatch (ADR-011).

## Action
Re-run scripts/train_pead_v2.py unchanged (same registration, same null) on the merged IPO-expanded panel once the bars ticket lands. PASS → PEAD returns to the book and Benzinga stays. FAIL → PEAD permanently closed; cancel Benzinga Earnings before the next billing cycle (purchase date 2026-06-10).

## Conversation

**2026-06-10 03:48 claude-code:** **Complete — FAIL, audited and upheld. PEAD permanently closed; recommend CANCELLING Benzinga Earnings before the next billing cycle** (purchased 2026-06-10; the 240,823-event history stays on disk regardless).

The retest ran with the registration unchanged on the repaired panel (131,115 events, 3× the original — the literature's small-cap segment genuinely tested for the first time: 96% of the bottom-ADV tercile is newly added names). Both variants fail every economic bar (A +0.414 / B +0.434 vs ≥+1.0 required; the crude proxy still wins the common window). Two findings worth keeping: (1) the permutation null flipped from unbeatable to cleanly beaten (z +2.30/+3.93) — small-name PEAD drift is *statistically real*, just tiny and cost-fragile (25bps costs → +0.34; a fifth of the book trades <$1M ADV names); (2) the delisted cohort earns ~zero — the old panel was survivorship-flattering PEAD. Adversarial audit rebuilt everything from scratch to identical numbers.

Bonus data fix discovered mid-run: `data/daily_bars_delisted/` had never been merged into the main panel — 4,407 reversible symlinks added and logged (verdict holds with or without them).
