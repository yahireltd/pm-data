---
id: T-0522
title: "Progress page: label the two times (finished vs effort) + add sorting"
type: feature
state: review
created: 2026-07-07T18:52:49Z
updated: 2026-07-07T19:09:59Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - The right-hand duration is labelled (took Xm / Xm so far) so it can't be misread as a timestamp
  - "Sort pills on the Progress page: Latest update (default), Recently started, Most effort — selectable via links, active one highlighted"
  - Sorting by Most effort ranks longest runs first (open runs count elapsed-so-far)
  - "Default behaviour unchanged: newest update first"
out_of_scope: []
code_anchors:
  - path: web/app/activity/page.tsx
    symbol: buildFeed / right column render
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260707-1853
    started: 2026-07-07T18:53:05Z
    status: completed
    ended: 2026-07-07T19:09:59Z
    summary: "The Progress page now shows one clear time per row. The confusing second value (how long each piece of work took — the \"29m\" under \"21d ago\") is gone entirely at Austin's request, rather than labelled: each row just shows when the work last moved, and hovering it says whether that was a finish (\"Finished 21d ago\") or a start (\"Started 5m ago — still going\"). The page also has two sort buttons — \"Latest update\" (the default) and \"Recently started\" — matching the two real timestamps a piece of work has. The \"Most effort\" sort from the first pass was dropped along with the duration display, since ordering by an invisible number would just bring the confusion back. Benefit: the feed reads at a glance with no second number to puzzle over, and you can flip between finished-order and started-order."
    test_plan: '1. After deploy, open Progress from the left nav. Each row shows ONE time on the right (e.g. "21d ago") — no "took Xm" or second value anywhere. 2. Hover a finished row → tooltip "Finished 21d ago"; hover an in-progress row → "Started Xm ago — still going". 3. Header shows exactly two pills: "Latest update" (default, highlighted) and "Recently started"; clicking swaps the order and the URL carries ?sort=started. 4. Regression: rows still link to tickets, summaries/status pills/project names unchanged, 100-row cap intact.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-07T19:09:59Z
version: 5
---

## Problem

Each Progress row shows two unlabelled values on the right — e.g. "21d ago" over "29m". They read as two conflicting timestamps, but they're actually WHEN the run last moved (finished, or started if still running) and HOW LONG it took (effort). Opening the ticket then shows created/updated (both 21d), which appears to contradict the row. Austin: "why does it say 29m and 21d and then when I click it shows just 21d... can we sort by created / last updated?"

## Design

- Label the second value: "took 29m" for finished runs, "29m so far" for running ones. Slightly widen the column.
- Add sort pills to the header (server-rendered, ?sort= links): Latest update (default, by finish/last-move time), Recently started (by run start), Most effort (by duration).

## Conversation

**2026-07-07 18:54 claude-code:** Run run-20260707-1853 completed — The two numbers on each Progress row now say what they mean, and the feed can be sorted. Before, every row showed something like "21d ago" with "29m" underneath and no explanation — it looked like two clashing timestamps, and clicking through to the ticket (created/updated 21 days ago) seemed to contradict it. In fact the top value is when that piece of work last moved (finished — or started, if still running) and the bottom one is how long the work took. The bottom value is now labelled — "took 29m" for finished work, "29m so far" for work still going — and hovering the column spells both out in a sentence. The page also gained three sort buttons: "Latest update" (the existing order), "Recently started", and "Most effort" (longest pieces of work first). If we'd left it, the feed would keep looking self-contradictory and there'd be no way to reorder it. Benefit: the Progress page reads unambiguously and can be sliced the way you're asking about it.
