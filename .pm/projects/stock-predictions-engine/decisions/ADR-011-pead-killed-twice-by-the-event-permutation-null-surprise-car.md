---
id: ADR-011
slug: pead-killed-twice-by-the-event-permutation-null-surprise-car
title: "PEAD killed twice by the event-permutation null: surprise carries no selection information in our universe"
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Fable 5
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
PEAD v2 on real frozen consensus (43,759 events, 2016–2026): variants A/B at +0.595/+0.539 net vs quarter-permutation null +0.574/+0.536 — z=+0.73/+0.04. Random same-size earnings-event subsets earn the same return: it's post-earnings holding drift, not surprise information. The crude YoY proxy (+1.04) beat real consensus (+0.60) on the identical window. Then the proxy itself faced the same null: book-window z=+0.22 (58th pctile), and the UNFILTERED all-events baseline (+1.134) beat the surprise-filtered sleeve (+0.994) — the filter subtracts value. Both results adversarially audited and upheld (reproductions bit-exact).

## Decision
PEAD (proxy and real-consensus) loses its book seat. Per pre-registered fallback the proxy was provisionally retained until its own null ran; it failed; book config sleeves.pead=false (commit c9fcab9).

## Consequences
The dry-run book is currently BB_Squeeze-only. One escape hatch remains, pre-registered: re-test PEAD v2 on the IPO-expanded small/mid-cap panel (121,677 event rows were unpriceable on the old panel; the literature places PEAD there). If that fails, PEAD is permanently closed and event-drift exposure, if wanted, must be booked honestly as unconditioned drift.