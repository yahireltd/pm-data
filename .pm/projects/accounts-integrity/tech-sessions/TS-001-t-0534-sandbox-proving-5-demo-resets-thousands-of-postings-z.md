---
id: TS-001
slug: t-0534-sandbox-proving-5-demo-resets-thousands-of-postings-z
title: "T-0534 sandbox proving: 5 demo resets, thousands of postings, zero orphans/duplicates"
project: accounts-integrity
created: 2026-07-10T21:44:57Z
updated: 2026-07-10T21:45:11Z
decisions:
  - Xero idempotency keys proven token-scoped by staged replay (created real duplicates in the demo org) — recovery architecture switched to log-driven heal + on-Xero backstop, never key replay (ADR-001). Reference where-filters on BankTransactions proven to silently match nothing — backstop matches client-side on description JSON.
actions:
  - text: "Scale of proving (for the record): ~5 full Demo Company resets, thousands of transactions posted across cycles, zero orphaned / zero duplicated; final completeness run 304/304 invoices, 109/109 payments, all allocation tables remaining=0, attempts==outcomes, empty duplicate census. Survived: 22h Retry-After hang scenario (now fail-fast + circuit breaker), mid-send interruptions recovered next run, token-refresh races (DB lock), and an unplanned 34-document mass rejection handled with nothing lost."
side_quests: []
problem: "The rebuilt posting pipeline (self-heal, backstops, mutexes, token store, cron+digest) had to be proven safe before touching live money — including under deliberate abuse: interrupted sends, rate-limit hangs, token refresh races, and re-posting the same data repeatedly."
success_criterion: Complete posting cycles on the Xero Demo Company with zero orphaned and zero duplicated records, all completeness counters reconciling, failures logged + digested rather than silent.
phase: test
version: 2
---

# TS-001: T-0534 sandbox proving: 5 demo resets, thousands of postings, zero orphans/duplicates

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
