---
id: ADR-012
slug: paper-loop-architecture-t212-practice-dry-run-default-reconc
title: "Paper-loop architecture: T212 practice, dry-run default, reconcile-blocks-execute, dead-man watchdog"
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Fable 5
  - Austin
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
kind: deployment
version: 1
---

## Context
The era-3 cron bot died silently when the host powered off (last run 2026-05-06, discovered 2026-06-09 with a stuck 34-day-old "1-day" paper position). The new book needs to run daily, fail loudly, and produce the live slippage record that gates real capital.

## Decision
src/live/book_signals.py + book_bot.py on Trading212 PRACTICE (acct 31204527): sleeve engines imported verbatim from the audited research scripts; config-toggled sleeves; ivol(63d, lag-1) weights; panel-coverage + per-ticker freshness guards refuse stale signals; broker-vs-state reconcile mismatch blocks --execute; fills logged through slippage_logger (3-way attribution); heartbeat every run. systemd user timers (Persistent=true): book-bot Mon–Fri 20:35 UTC (installed, dry-run, awaiting user enable), book-watchdog daily (live) → ntfy topic austin-quant-book-b4b92040 + ALERT_STALE flag if heartbeat >30h (78h weekends).

## Rollback
`systemctl --user disable --now book-bot.timer` is the kill switch; EXECUTE=1 is a separate explicit step beyond enabling the timer; positions reconcile on every run so a killed bot leaves an auditable state.