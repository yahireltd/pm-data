---
id: T-0338
title: Merge daily_bars_ipo + _stale_patch into the main bar panel; one canonical refresh path
type: chore
state: done
created: 2026-06-10T01:50:57Z
updated: 2026-06-13T17:00:24Z
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
  - "[x] data/daily_bars/ max date == last trading day for >=95% of active names"
  - "[x] RKLB, IONQ, ASTS, APP present with full histories"
  - "[x] Single documented refresh command; partial-refresh root cause fixed"
out_of_scope: []
code_anchors:
  - path: scripts/backfill_daily_bars.py
    symbol: defaults to files on disk, not 106-name seed list
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260613-1638
    model: claude-opus-4-8
    started: 2026-06-13T16:38:35Z
    status: completed
    ended: 2026-06-13T16:38:59Z
    summary: "The price-history database was incomplete: a daily refresh had only been updating about 106 names instead of the full ~1,300, so most stocks showed stale prices (one famously frozen months out of date). Root cause was found and fixed on 2026-06-10 — the refresh now reads every stock on disk rather than a hardcoded shortlist. Missing newer listings and stale tails were filled in (≈39,400 new price points plus 4,748 newly-listed names), and the master universe was rebuilt through 2026-06-09. All spot-checked names are now current; the only remaining gaps are dead/delisted stocks Polygon no longer publishes. Without this fix the trading system would have been making decisions on months-old prices. Finalizing original work; tracker was never moved off the backlog."
    test_plan: 1. Confirm `data/daily_bars/` max date equals the last trading day for the large majority of active names. 2. Spot-check RKLB, IONQ, ASTS, APP, MRVL have full, current histories. 3. Confirm there is a single documented refresh command and re-running it does not leave names stale.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: fix committed in ec02bad; data-quality audit report committed 655d640
labels:
  - data
  - infra
attention: null
version: 11
---

## Problem
The 2026-06-10 refresh proved partial: 1,242/1,329 names stale after 2026-04-23 (MRVL famously ended 2026-03-31 at $99 while trading ~$300 live). The IPO-gap workflow is writing missing post-2016 listings to `data/daily_bars_ipo/` and stale tails to `_stale_patch/` (no-clobber while other jobs read the main panel).

## Action
When the IPO workflow completes: merge both dirs into `data/daily_bars/`, rebuild the PIT universe, fix whatever made `backfill_daily_bars.py` partial (ticker-list source vs full panel), and make the paper bot's coverage guard the canonical freshness check.

## Conversation

**2026-06-10 03:28 claude-code:** **Complete — ready to close.** Root cause: the refresh script iterated a hardcoded ~106-name seed list instead of the files on disk (fixed, committed). Stale patch: 39,412 new bars across 1,365 files, 0 errors. IPO merge: 4,748 names in (1 collision, resolved). Rebuilt: PIT universe (14.05M rows, 6,023 survivors + 4,497 delisted, to 2026-06-09), monthly top-50 universe files, ADV panel. All spot names (RKLB/IONQ/ASTS/APP/MRVL/SNDK/SPY/AAPL) current. The 692 remaining "laggards" are dead listings with no newer data on Polygon — coverage among living names is 100%. Tonight's 21:35 bot run should pass its freshness guard. PEAD small-cap retest (T-0339) firing now.

**2026-06-13 16:38 claude-code:** Run run-20260613-1638 completed — The price-history database was incomplete: a daily refresh had only been updating about 106 names instead of the full ~1,300, so most stocks showed stale prices (one famously frozen months out of date). Root cause was found and fixed on 2026-06-10 — the refresh now reads every stock on disk rather than a hardcoded shortlist. Missing newer listings and stale tails were filled in (≈39,400 new price points plus 4,748 newly-listed names), and the master universe was rebuilt through 2026-06-09. All spot-checked names are now current; the only remaining gaps are dead/delisted stocks Polygon no longer publishes. Without this fix the trading system would have been making decisions on months-old prices. Finalizing original work; tracker was never moved off the backlog.

---

**2026-06-13 17:00 — you**

done
