---
id: T-0341
title: BB_Squeeze restatement on the PIT universe (it now carries the whole book)
type: spike
state: review
created: 2026-06-10T01:51:27Z
updated: 2026-06-13T21:17:22Z
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
    status: completed
    ended: 2026-06-13T21:17:22Z
    summary: "We re-tested the trading system's only remaining live strategy (\"BB_Squeeze\") honestly, on real data, fixing two ways the old result had been flattering itself: it now uses a fair list of stocks that includes ones that later went bust (not just today's survivors), and it sells positions the way the live bot actually does. The famous \"1.73\" score fell to 0.72. More importantly, we ran the decisive test: we compared the strategy's stock-picking to picking stocks at RANDOM and managing them the same way. They scored the same (0.72 vs 0.71). In plain terms: the strategy's clever entry signal adds nothing — its returns come from simply being invested in a rising market with a favorable exit rule, not from any real edge. The one ML signal (S2) separately passed its cost test but is very thin. Conclusion: the book currently has no strategy that beats random chance by a meaningful margin, so committing real money is not justified yet. This is the honest result the test was built to find before any capital was at risk."
    test_plan: "1. Confirm the config was frozen identical to the 2026-06-10 run — only universe (PIT delisted-inclusive) and exit convention (close) changed, no parameter re-tuning (see out_of_scope). 2. Verify the provenance ladder in memory/bb_pit_restatement_results.md: 1.726 → 0.801 → 0.718. 3. Verify the entry-permutation null: REAL 0.718 vs null 0.708±0.413, z=+0.02 (bb_pit100_close_null.csv on the box). 4. Confirm adv_monthly was rebuilt delisted-inclusive (4,263 delisted) before the PIT rebuild. 5. Decision to ratify: EXECUTE=1 (MS-015) on hold — see the new ADR."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: memory/bb_pit_restatement_results.md committed f621e19 on branch t0341-bb-pit-close-exits
labels:
  - research
  - book
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-13T21:17:22Z
version: 6
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

**2026-06-13 17:37 claude-code:** **2026-06-13 — restatement run on real data (austin-ubuntu, repaired panel).** Code committed on branch `t0341-bb-pit-close-exits`; PIT universe rebuilt to the pre-registered top-100 (78 monthly files); config frozen identical to the 2026-06-10 run (h10, rebal 5, TP10/SL5, 3 pos, 10bps).

**Headline: BB_Squeeze honest Sharpe = 0.80** (791 days, 2023-01→2026-02, +51% total) — down from the 1.73 headline. The book's only live sleeve loses ~54% of its Sharpe once the flattery is removed.

**Attribution (what caused the drop):**
- current-name universe + touch-fill (old): **1.726**
- PIT delisted-inclusive universe + touch-fill: **0.821**  → survivorship/universe costs **−0.90**
- PIT universe + close-based exits (headline): **0.801**  → exit convention costs only **−0.02**

So ~98% of the collapse is the **survivorship-tinted hand-picked universe**, not the touch=fill assumption we worried about. Same lesson as PEAD: the old current-name list was flattering the result. Close-vs-touch exit divergence is negligible for this strategy.

**Still running / pending acceptance:** 200-run entry-permutation null (in progress) → does 0.80 beat random same-size entries through the identical machinery; delisted-cohort contribution breakout; adversarial re-derivation. **Implication if 0.80 holds:** the BB-only book sits below the charter's ≥1.0 target on its own — sleeve #2 (S2 h20 / S11) and the EXECUTE=1 decision (MS-015) need to weigh this.

**2026-06-13 21:17 claude-code:** Run run-20260613-1712 completed — We re-tested the trading system's only remaining live strategy ("BB_Squeeze") honestly, on real data, fixing two ways the old result had been flattering itself: it now uses a fair list of stocks that includes ones that later went bust (not just today's survivors), and it sells positions the way the live bot actually does. The famous "1.73" score fell to 0.72. More importantly, we ran the decisive test: we compared the strategy's stock-picking to picking stocks at RANDOM and managing them the same way. They scored the same (0.72 vs 0.71). In plain terms: the strategy's clever entry signal adds nothing — its returns come from simply being invested in a rising market with a favorable exit rule, not from any real edge. The one ML signal (S2) separately passed its cost test but is very thin. Conclusion: the book currently has no strategy that beats random chance by a meaningful margin, so committing real money is not justified yet. This is the honest result the test was built to find before any capital was at risk.
