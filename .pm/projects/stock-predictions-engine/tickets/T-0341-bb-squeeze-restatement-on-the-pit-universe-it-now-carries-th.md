---
id: T-0341
title: BB_Squeeze restatement on the PIT universe (it now carries the whole book)
type: spike
state: in_progress
created: 2026-06-10T01:51:27Z
updated: 2026-06-13T17:12:51Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Restatement run with the config frozen identical to the 2026-06-10 run; only universe (PIT delisted-inclusive, pre-registered top-N) and exits (close-based) changed — no re-tuning to recover Sharpe
  - Restated Sharpe + daily-return series produced with a full provenance line in memory/nonml_book_results.md (script, SHA, input hashes, cost, universe+N, dates)
  - Entry-signal permutation null reported (random same-size entries through identical close-based exits, N>=200); touch-vs-close divergence and delisted-cohort contribution broken out
  - Independent adversarial audit reproduces the number from scratch; EXECUTE=1 book sizing updated to the restated number (or written verdict if the sleeve collapses)
out_of_scope:
  - Re-tuning tp/sl/hold/positions/top-N to recover the Sharpe after seeing results
  - Reverting to or keeping the hardcoded current-name TRADE_TICKERS list
  - Changing the BB squeeze entry rule
  - Building a new parquet universe format (reuse the existing monthly CSVs)
  - Any new strategy family (moratorium until 2026-07-15)
code_anchors:
  - path: scripts/ws_momentum_strategies.py
    symbol: run_walk_forward / strat_bb_squeeze
    lines: 820-850, 1142-1151, 54-63
  - path: scripts/nonml_book_build_sleeves.py
    symbol: build_bb + univ_fn (MR) pattern
    lines: 120-149, 60-85
  - path: scripts/build_pit_universe.py
    symbol: monthly top-N ADV PIT universe
  - path: src/live/book_signals.py
    symbol: close-based exit + two-convention divergence log
    lines: 227-243
  - path: scripts/v4_nulls.py
    symbol: shuffle/dirichlet null harness
  - path: memory/nonml_book_results.md
    symbol: provenance table to update
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260613-1712
    model: claude-opus-4-8
    started: 2026-06-13T17:12:51Z
    status: in_progress
labels:
  - research
  - book
attention: null
version: 4
---

## Problem
BB_Squeeze (+1.726 @10bps, 838 days, 2023-01→2026-03) is now the **sole** book sleeve — PEAD permanently closed (ADR-011, T-0339) and MR dropped (charter Amendment 1). But that headline was produced on a **survivorship-tinted ~96-name current-name list** with an **intrabar touch=fill** exit model, while the live bot exits **at the close**. The entire paper book rests on a flattering number computed two ways the bot can't reproduce. It must be restated honestly before EXECUTE=1 sizing (MS-015, 2026-06-24).

This is a **restatement of an existing sleeve, not a new strategy family** → not blocked by the 2026-07-15 moratorium.

## Current implementation (provenance of the 1.726)
- Engine: `scripts/ws_momentum_strategies.py::run_walk_forward` — entry `strat_bb_squeeze` (≈L820-850), exit harness ≈L1142-1151.
- **Exit = touch-fill**: SL fills at `entry*(1-sl)` when `low` touches; TP fills at `entry*(1+tp)` when `high` touches; time-stop at close. No gap-through modeling.
- **Universe = hardcoded `TRADE_TICKERS`** (~96 current names, `ws_momentum_strategies.py` ≈L54-63) — NOT PIT.
- Driver/config: `scripts/nonml_book_build_sleeves.py::build_bb` — `h10, rebal 5, tp 10%, sl 5%, max_positions 3`; output `data/processed/book/sleeve_bb_squeeze_h10_10bps.csv`; provenance in `memory/nonml_book_results.md` (SHA 1fe274b, 10bps RT).

## Pre-registration (freeze BEFORE running — charter numbers policy)
Zero free parameters. The config is frozen **identical** to the 2026-06-10 run (`h10, rebal 5, tp 10%, sl 5%, max 3, 10bps RT`). Exactly **two** changes, both honesty-restoring, neither tunable:
1. **Universe → PIT, delisted-inclusive.** Use `scripts/build_pit_universe.py` output (`data/universe_pit/{YYYY-MM-DD}.csv`, monthly top-N by trailing-60d ADV) consumed via the `univ_fn` lookup pattern already used for MR (`nonml_book_build_sleeves.py` ≈L60-85). Pull bars from the merged delisted-inclusive panel (`data/daily_bars/` post-T-0338, incl. _ipo/_stale_patch/_delisted). **Top-N is pre-registered, not swept**: pick the single N whose breadth best matches the current ~96 names (candidate **top-100**) and commit it before seeing any Sharpe.
2. **Exits → close-based**, as the live bot trades (`src/live/book_signals.py` ≈L227-243). TP/SL trigger when the *close* crosses the threshold and fill at that close, not the intrabar touch.

Explicitly forbidden: re-tuning tp/sl/hold/positions/N to recover the Sharpe after seeing it drop; reverting to the current-name list; switching the entry rule.

## Nulls (report alongside the restated Sharpe)
- **Entry-signal permutation**: random same-size entries drawn from the same PIT universe, run through the *identical* close-based exit machinery — does the squeeze entry beat random timing?
- **Shuffle / Dirichlet** timing nulls via `scripts/v4_nulls.py` machinery if BB is later weighted in a blend.
- Pre-register N≥200 perms, fixed seed, fresh RNG per window (pattern in `scripts/proxy_pead_null_test.py` / `scripts/exit_policy_study.py`).

## Deliverables
- Restated daily-return series + Sharpe on PIT/close-based, full provenance line (script, git SHA, input hashes, cost model, universe+N, date range) appended to `memory/nonml_book_results.md`.
- **Touch-vs-close divergence**: report the gap between the old touch-fill number and the restated close number (sanity-cross-check against the live bot's two-convention logs once enough fills exist).
- Delisted-cohort contribution broken out (did survivorship flatter it, as it did PEAD?).
- Independent adversarial audit rebuilding the result from scratch to identical numbers.
- Book sizing for EXECUTE=1 updated to the restated number (or, if it collapses, a written verdict on whether the book has a sleeve at all).

## Conversation

**2026-06-13 claude-code:** Scoped. Code map confirms BB still runs on the hardcoded ~96-name list with touch-fill exits; PIT machinery (`build_pit_universe.py` + the MR `univ_fn` pattern) and close-based exit logic (`book_signals.py`) both already exist to reuse. Two frozen changes (PIT delisted-inclusive universe; close-based exits), no re-tuning. Ready to mark ready/claim when the bot-stale issue is cleared.
