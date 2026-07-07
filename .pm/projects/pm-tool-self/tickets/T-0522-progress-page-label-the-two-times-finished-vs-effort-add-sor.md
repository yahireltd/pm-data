---
id: T-0522
title: "Progress page: label the two times (finished vs effort) + add sorting"
type: feature
state: review
created: 2026-07-07T18:52:49Z
updated: 2026-07-07T18:54:50Z
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
    ended: 2026-07-07T18:54:50Z
    summary: "The two numbers on each Progress row now say what they mean, and the feed can be sorted. Before, every row showed something like \"21d ago\" with \"29m\" underneath and no explanation — it looked like two clashing timestamps, and clicking through to the ticket (created/updated 21 days ago) seemed to contradict it. In fact the top value is when that piece of work last moved (finished — or started, if still running) and the bottom one is how long the work took. The bottom value is now labelled — \"took 29m\" for finished work, \"29m so far\" for work still going — and hovering the column spells both out in a sentence. The page also gained three sort buttons: \"Latest update\" (the existing order), \"Recently started\", and \"Most effort\" (longest pieces of work first). If we'd left it, the feed would keep looking self-contradictory and there'd be no way to reorder it. Benefit: the Progress page reads unambiguously and can be sliced the way you're asking about it."
    test_plan: "1. After deploy, open Progress from the left nav. Each row's right column now reads e.g. \"21d ago\" with \"took 29m\" beneath; hover it — the tooltip says \"Finished 21d ago — the run took 29m\". 2. Find a ticket with an agent currently working (In progress pill): its row should say \"Xm so far\" instead of \"took Xm\". 3. Click the three pills in the header: \"Latest update\" (default — same order as before), \"Recently started\" (rows reorder by when work began), \"Most effort\" (longest runs first). The active pill is highlighted, and the URL carries ?sort= so it's shareable/refreshable. 4. Cross-check the row that prompted this (T-0356, \"21d ago / 29m\"): its ticket's comment timeline (claim 21:06 minus start 20:37) matches the 29m. 5. Regression: rows still link to their tickets, project names still show, the 100-row cap and access scoping unchanged."
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
  since: 2026-07-07T18:54:50Z
version: 4
---

## Problem

Each Progress row shows two unlabelled values on the right — e.g. "21d ago" over "29m". They read as two conflicting timestamps, but they're actually WHEN the run last moved (finished, or started if still running) and HOW LONG it took (effort). Opening the ticket then shows created/updated (both 21d), which appears to contradict the row. Austin: "why does it say 29m and 21d and then when I click it shows just 21d... can we sort by created / last updated?"

## Design

- Label the second value: "took 29m" for finished runs, "29m so far" for running ones. Slightly widen the column.
- Add sort pills to the header (server-rendered, ?sort= links): Latest update (default, by finish/last-move time), Recently started (by run start), Most effort (by duration).

## Conversation

**2026-07-07 18:54 claude-code:** Run run-20260707-1853 completed — The two numbers on each Progress row now say what they mean, and the feed can be sorted. Before, every row showed something like "21d ago" with "29m" underneath and no explanation — it looked like two clashing timestamps, and clicking through to the ticket (created/updated 21 days ago) seemed to contradict it. In fact the top value is when that piece of work last moved (finished — or started, if still running) and the bottom one is how long the work took. The bottom value is now labelled — "took 29m" for finished work, "29m so far" for work still going — and hovering the column spells both out in a sentence. The page also gained three sort buttons: "Latest update" (the existing order), "Recently started", and "Most effort" (longest pieces of work first). If we'd left it, the feed would keep looking self-contradictory and there'd be no way to reorder it. Benefit: the Progress page reads unambiguously and can be sliced the way you're asking about it.
