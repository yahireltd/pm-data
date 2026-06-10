---
id: T-0343
title: Exit-policy study verdict → skew-sleeve charter decision (post 2026-07-15)
type: spike
state: triaged
created: 2026-06-10T01:51:40Z
updated: 2026-06-10T02:05:09Z
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
  - Study results + adversarial audit reviewed
  - Written keep/park decision; if keep, charter amendment drafted with pre-registered G4-style admission
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
  - charter
attention: null
version: 2
---

## Problem
User's stated pain: held RKLB/PLTR/SNDK-class names and sold too soon, missing ~1000% runs. Both had >50% mid-run drawdowns — any tight stop guaranteed the miss. The exit-policy study (in flight: 52w-high breakouts on the IPO-expanded panel, +50%/+100% TPs vs 30/40-week-MA and 3×ATR trails, delisted-inclusive, adversarially audited) quantifies what each policy captures of ≥100%/≥500%/≥1000% winners.

## Action
When the study + audit land: review results; if trend-trails materially dominate take-profits, draft the skew-sleeve charter amendment (lottery-ticket sizing 0.5–1%, trend exits, judged on terminal wealth + tail capture, NOT Sharpe) — moratorium allows it from 2026-07-15.

## Conversation

**2026-06-10 02:05 claude-code:** Study delivered and adversarially audited (upheld, minor issues) — commit `da9f1bc`, full tables in `memory/exit_policy_study_results.md`.

Headline, against expectations: **the +50% take-profit was NOT the costly behavior** — it beats trend trails outright on average (+7.2%/trade vs +2.5% for the 40-week SMA and +0.5% for 3×ATR), because trend stops chop out the 87% of breakouts that never double and exit a third of true winners at a loss. The real cost of +50% was capping the 50→100%+ band: ~22% of total P&L given up vs a +100% take-profit or an 18-month hold.

The 1000% monsters (RKLB/PLTR class) are 0.6% of entries and uncatchable inside ANY 18-month policy — they only pay through multi-year, no-time-stop trend holds, which as a policy have a 37% win rate and a −6% median trade (the winners carry 3× the entire P&L).

Decision now teed up for after the 2026-07-15 moratorium: a two-pocket design — core entries at a +100% TP, plus a small (0.5–1%) lottery pocket riding the 40-week SMA with no time stop, judged on terminal wealth only. The hybrid (bank half at +100%, ride the rest) is the pre-registered candidate to TEST — it was not selected post-hoc from tonight's table.
