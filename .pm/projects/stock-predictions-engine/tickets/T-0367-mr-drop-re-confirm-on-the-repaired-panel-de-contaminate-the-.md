---
id: T-0367
title: MR drop re-confirm on the repaired panel (de-contaminate the Amendment-1 verdict)
type: spike
state: triaged
created: 2026-06-13T17:07:11Z
updated: 2026-06-13T17:07:11Z
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
  - MR re-run with the Amendment-1 spec frozen identical; only the input panel changed to the repaired delisted-inclusive build (data/universe_pit/ + ADV rebuilt post-T-0338)
  - Restated standalone Sharpe + book-impact evaluated against the unchanged Amendment-1 AND-rule, committed with full provenance (script, SHA, input hashes, cost, universe, dates)
  - "Verdict recorded against ADR-007: upholds the drop → confirming note; flips to PASS → flagged for human review, no auto-readmit"
out_of_scope:
  - Re-tuning the MR spec (top-N, holding, entry rule) to recover the Sharpe
  - Auto-readmitting MR to the book on a PASS without human review
  - Re-running S9 or the 3 ML FAILs (cost/lag-methodology calls, not bar-panel-dependent)
  - Extending the evaluation window past the frozen builder END date
code_anchors:
  - path: scripts/mr_pit_validation.py
    symbol: run_strategy
  - path: scripts/build_pit_universe.py
    symbol: monthly top-N ADV PIT universe (rebuild on repaired panel)
  - path: memory/nonml_book_results.md
    symbol: MR provenance row + cost sensitivity (the contaminated verdict)
  - path: docs/CHARTER_90D.md
    symbol: Amendment 1 — MR drop/keep rule
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - research
  - charter
  - data-hygiene
attention: null
version: 1
---

## Problem
MR was dropped from the paper book under charter Amendment 1 on 2026-06-10 (+0.118 PIT top-30 @10bps vs the required ≥+0.30, decision mechanical). But the verdict was computed **before the T-0338 panel repair landed**: the MR commit is 00:55/01:48, while the data-quality audit (655d640) is 03:19 and the backfill root-cause fix (ec02bad) is 04:28. Per `memory/nonml_book_results.md:14`, MR's PIT universe was rebuilt that same night from a panel still missing 4,748 IPO names, 39,412 stale-tail bars, and the 4,407-symlink delisted cohort. By the project's own numbers policy, a Sharpe computed on data we've since shown was wrong is contaminated — the verdict of record rests on a superseded panel.

PEAD got a clean post-repair retest (T-0339, still FAIL, audited); MR did not. This ticket closes that one gap.

## Expectation (stated up front, not a target)
Adding the delisted cohort to a long-the-most-oversold-3 strategy means catching names that later went to zero — the expected direction is **MR stays dead or gets worse**, exactly as PEAD degraded on the repaired panel. This is run to **de-contaminate the verdict**, not to rescue MR. A PASS would be genuinely surprising and would itself warrant scrutiny before any book change.

## Action
Re-run the **frozen Amendment-1 spec, zero re-tuning**: `scripts/mr_pit_validation.py::run_strategy`, XS 1-day MR long bottom-3, next-close exit, PIT top-30 by trailing-60d ADV, monthly rebuilt, 10bps RT, concatenated daily incl. flat days. The only change vs the 2026-06-10 run is the **input panel**: rebuild `data/universe_pit/` and ADV from the repaired delisted-inclusive panel (post-T-0338), then evaluate against the unchanged Amendment-1 AND-rule (standalone ≥+0.30 AND improves book Sharpe or MaxDD). Commit with full provenance either way.

## Decision
- Verdict upholds the drop (expected) → record as a confirming note on ADR-007; MR stays parked (G4 re-entry only with a changed spec).
- Verdict flips to PASS → do **not** auto-readmit; flag for human review, since a survivorship-repair that *improves* a long-oversold sleeve is counterintuitive and likely a data artifact to investigate.

## Context
Low priority by design. Sequencing is **bot-stale fix → T-0341 (BB restatement, the survivor actually at risk of being flattered) → this**. S9 and the 3 ML FAILs from the same pre-repair batch are cost/lag-methodology calls, far less dependent on the bar panel — out of scope here.
