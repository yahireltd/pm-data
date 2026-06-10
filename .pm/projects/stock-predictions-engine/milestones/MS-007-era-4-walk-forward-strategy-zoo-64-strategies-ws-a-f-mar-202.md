---
id: MS-007
slug: era-4-walk-forward-strategy-zoo-64-strategies-ws-a-f-mar-202
title: "Era 4: Walk-forward strategy zoo — 64 strategies, WS A–F (Mar 2026) — Opus 4.6, parallel agents"
project: stock-predictions-engine
state: hit
order: 3072
created: 2026-06-10T01:47:02Z
updated: 2026-06-10T01:48:27Z
acceptance_criteria:
  - walk_forward_true.py harness + macro/commodity features; ~35 commits in one month, largely agent-parallel
  - 64 strategies across momentum/PEAD/BB_Squeeze/MTF/stat-arb v1–5/vol suite (95)/alt-data (144 features)/sector rotation
  - "Peak contamination: 'OOS Sharpe 14.38' (mean-of-window bug) and MR+PEAD 2.68 (positional combination) — both later killed"
  - "Honest in-stream negatives logged too: WS10 stress test showed raw MR loses at 5bps; stat arb declared dead after 5 versions"
slip_records: []
target_date: 2026-03-28
phase: build
version: 2
---

# Era 4: Walk-forward strategy zoo — 64 strategies, WS A–F (Mar 2026) — Opus 4.6, parallel agents

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
