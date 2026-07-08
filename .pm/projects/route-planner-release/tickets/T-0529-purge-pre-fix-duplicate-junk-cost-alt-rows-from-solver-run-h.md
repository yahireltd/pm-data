---
id: T-0529
title: Purge pre-fix duplicate/junk cost_alt rows from solver run history
type: chore
state: review
created: 2026-07-08T17:08:29Z
updated: 2026-07-08T17:12:05Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - History page for Fri 10 Jul 2026 shows one card per real solve (full_luton primaries), no blank/duplicate-ID cards
  - Row counts logged before/after the delete (in ticket comment)
  - solver_runs table row count unchanged
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerPlanService.php:1801-1900
    note: the fixed cost_alt persistence (context)
  - path: backend/views/route-planner/history.php
    note: history cards render from solver_run_history
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1710
    model: claude-fable-5
    started: 2026-07-08T17:10:40Z
    status: completed
    ended: 2026-07-08T17:12:05Z
    summary: "Cleaned the solver run history on the sandbox database. Removed 432 junk cards from the history page: 30 duplicate \"Manual\" twins (the cost_alt plan mislabelled as a normal run with 0 contracts) and 402 pre-fix cost-alternative cards, which under the old selection bug always showed plans that dropped more jobs than the real one. History went from 1,844 rows to 1,412; the underlying solver_runs data (2,457 rows) is untouched, so any old run can still be replayed. Solves from today's fixed code (single primary card + at most one properly-badged \"Cost alt\" card, both after 16:00) were preserved."
    test_plan: |-
      1. Open /route-planner/history and check Fri 10 Jul 2026: the morning solves (13:56–14:31) now show one full_luton card each — no plain/blank-badge duplicates, no "Manual" cards with 0 contracts.
      2. Spot-check an older date that had cost_alt cards — only the primary (split-labelled) cards remain.
      3. Replay/Sketch buttons on remaining cards still work (solver_runs untouched).
      4. Cards created after 16:00 today (the post-fix re-solves) are still present.
      Note: sandbox DB only — if the live DB accumulates the same historical junk before release, re-run the same two deletes there (script preserved at /tmp/history_cleanup.php on the sandbox box; counts logged in this run).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - data-cleanup
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-08T17:12:05Z
version: 4
---

## Problem
Before the 2026-07-08 fix, every solve with a cost_alt wrote three solver_run_history rows: the primary (Manual + split label), a mislabelled "Manual" twin of the cost_alt (no split label, 0 contracts), and a blank-badge cost_alt row. Under the old seed-selection bug those cost_alt plans also always dropped MORE jobs, so the old cards are junk. Austin: "yes lets clear up those old bad plans".

## Fix (data-only, sandbox DB)
1. Delete `source='manual'` history rows whose solver_run_id also has a `source='cost_alt'` history row (the mislabelled twins — post-fix runs don't create these, so the join only matches old pairs).
2. Delete `source='cost_alt'` history rows created before 2026-07-08 16:00 (drop-heavy junk by construction under the old selection rule).

solver_runs rows are NOT touched — full replay data is preserved; only the history-page cards are curated. Post-fix solves already write exactly one primary + at most one Cost alt card.

## Conversation

**2026-07-08 17:12 claude-code:** Run run-20260708-1710 completed — Cleaned the solver run history on the sandbox database. Removed 432 junk cards from the history page: 30 duplicate "Manual" twins (the cost_alt plan mislabelled as a normal run with 0 contracts) and 402 pre-fix cost-alternative cards, which under the old selection bug always showed plans that dropped more jobs than the real one. History went from 1,844 rows to 1,412; the underlying solver_runs data (2,457 rows) is untouched, so any old run can still be replayed. Solves from today's fixed code (single primary card + at most one properly-badged "Cost alt" card, both after 16:00) were preserved.
