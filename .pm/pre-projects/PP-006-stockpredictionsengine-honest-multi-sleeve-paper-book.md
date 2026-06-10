---
id: PP-006
slug: stockpredictionsengine-honest-multi-sleeve-paper-book
title: StockPredictionsEngine — honest multi-sleeve paper book
state: ready
created: 2026-06-10T01:12:57Z
updated: 2026-06-10T01:13:30Z
stakeholders: []
source_tickets: []
summary: "Quantitative US-equities research system (Polygon data, LightGBM specialists, event sleeves) being run to an audited, pre-registered standard after repeated contaminated-headline cycles (14.38 → 2.5 → 1.86 all killed by audits). Current honest state: BB_Squeeze +1.73, PEAD-proxy +1.05, ML 3-sleeve blend +0.37; daily paper loop on Trading212 practice built and dry-running; PEAD v2 on real consensus data in flight. Goal: a 2-4 sleeve paper book with 60 days of live fill/slippage records and a gated decision on first real capital (≤£2k) per docs/CHARTER_90D.md."
owner:
  kind: human
  name: Austin
version: 2
problems:
  - Nine months of research repeatedly produced contaminated headline Sharpes (14.38, 3.55, 2.68, 2.51, 1.86, 0.957) that later audits killed — mean-of-window Sharpe, in-sample universe selection, residual-return circularity, unlagged gates
  - Laptop and server research lines diverged for 6 weeks (each blind to the other's results); make-or-break runs sat unexecuted for 11 days with no mechanism noticing
  - Only thin validated edges exist (BB_Squeeze +1.73, PEAD-proxy +1.05, ML blend +0.37) and none are deployed — no live fill/slippage record exists to validate any cost model
  - "Operational decay: cron bot died silently when the machine powered off, leaving a stuck paper position for 34 days; GPU driver broken for weeks unnoticed"
goals:
  - Run a 2-4 sleeve daily paper book on Trading212 practice with every number honest by construction (pre-registered configs, lagged signals, PIT universe, nulls)
  - Accumulate ≥60 trading days of live fills with 3-way slippage attribution
  - Upgrade PEAD from YoY proxy to real frozen consensus (Benzinga, $99/mo) — the highest audited-EV ML move
  - Reach a mechanical, pre-registered go/no-go on first real capital (≤£2k) via charter gate G3
scope_in:
  - Daily paper trading loop (T212 practice), systemd timers, dead-man watchdog
  - PEAD v2 on Benzinga consensus; EDGAR Form-4 insider backfill + S10 re-run
  - Post-2016 IPO bar backfill; exit-policy (sold-too-soon) research study
  - "Charter governance: pre-registered acceptance bars, ADRs for every kill/adopt decision"
scope_out:
  - Real-capital deployment before gate G3 passes (explicitly out until ≥60 paper days)
  - Intraday RL/PPO execution stack (dead since Nov 2025)
  - Options-based signals in any form (closed 0-for-3 by audit)
  - New strategy families beyond the three charter-pre-listed exceptions before 2026-07-15
  - Headline numbers without registry provenance
success_criteria:
  - Book runs ≥60 consecutive trading days with zero silent failures (watchdog-verified)
  - Realized round-trip slippage ≤1.25x modeled on ≥100 fills
  - PEAD v2 verdict delivered against its pre-registered acceptance (≥+1.0 Sharpe, n≥2000, beats proxy, 2σ null) — PASS or FAIL both count as success if honest
  - G3 capital decision made mechanically from the charter, not vibes
cost_of_inaction: "Continued research-in-circles: more contaminated numbers, zero live evidence, no path to deploying real capital. Each undocumented cycle re-litigates killed ideas (28 binary regime filters, 6 stat-arb versions, 3 options attempts). Without a live slippage record every backtest cost assumption stays unfalsifiable, so no honest go/no-go on real money is ever possible."
milestones:
  - title: Foundation truth established
    acceptance_criteria:
      - Meta rerun + realistic-cost rescore executed with nulls
      - All contaminated headlines retired in code/docs
      - Git lines reconciled and pushed
    target_date: 2026-06-10
  - title: Paper loop live in dry-run
    acceptance_criteria:
      - book-bot.timer enabled and producing daily signals + heartbeats
      - Watchdog alerting verified end-to-end
    target_date: 2026-06-13
  - title: PEAD v2 verdict
    acceptance_criteria:
      - Pre-registered acceptance evaluated on full Benzinga history
      - Adversarial audit upholds the verdict
      - "If PASS: proxy sleeve replaced in book config"
    target_date: 2026-06-14
  - title: Paper book executing (EXECUTE=1)
    acceptance_criteria:
      - Real practice orders submitted daily
      - Slippage log accumulating ≥5 fills/week
      - Broker reconciliation clean 10 consecutive days
    target_date: 2026-06-20
  - title: G3 capital gate review
    acceptance_criteria:
      - ≥60 paper days banked
      - Slippage ≤1.25x modeled
      - Max DD ≤1.5x backtest
      - Written go/no-go decision as ADR
    target_date: 2026-09-07
workstream_ownership:
  - workstream: Research & validation
    owner: Claude (Fable 5)
  - workstream: Infrastructure & paper loop
    owner: Claude (Fable 5)
  - workstream: Capital & purchase decisions, timer enables
    owner: Austin
kickoff_held: true
scoping_held: true
---

# PP-006: StockPredictionsEngine — honest multi-sleeve paper book

## Summary

Quantitative US-equities research system (Polygon data, LightGBM specialists, event sleeves) being run to an audited, pre-registered standard after repeated contaminated-headline cycles (14.38 → 2.5 → 1.86 all killed by audits). Current honest state: BB_Squeeze +1.73, PEAD-proxy +1.05, ML 3-sleeve blend +0.37; daily paper loop on Trading212 practice built and dry-running; PEAD v2 on real consensus data in flight. Goal: a 2-4 sleeve paper book with 60 days of live fill/slippage records and a gated decision on first real capital (≤£2k) per docs/CHARTER_90D.md.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
