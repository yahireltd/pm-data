---
id: T-0319
title: Developer details
type: feature
state: triaged
priority: p2
created: 2026-06-09T17:05:07Z
updated: 2026-06-09T20:48:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 94208
reporter: null
assignee: null
acceptance_criteria:
  - an update to this ticket. with what it will take to build this
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - concurrency-demo
  - round-2
attention: null
version: 3
---

In the help section of tickets there is the following text: \
:What you'll see

* **Developer details** — tucked away behind a click: the history of attempts, the related work, and the exact spots in the code that were touched.

I can not see this but it would be a very useful feature do we have the feature already and if not can we look into building it

test

## Conversation

**2026-06-09 17:22 claude-code:** **Good news — this feature already exists; it was just hidden, which is why you couldn't see it.**

**What we found:** the "Developer details" panel the help text describes (the attempt history, the related work, and the exact code spots touched) is already built and live. On a ticket it shows as a collapsed **Developer details** section you click open — with the work timeline, the code anchors, related/blocking tickets, and any sub-tickets.

**Why it wasn't showing for you:** it's deliberately hidden in two cases — (1) when you're viewing as a stakeholder, and (2) when a ticket has no developer activity yet (no attempts logged, no code touched, no links). This ticket is brand-new with none of that, so there was genuinely nothing to put in it.

**So it isn't missing — it's a discoverability gap:** the help text promises the panel unconditionally, but the screen hides it with no "nothing here yet" placeholder, so it reads as absent.

**What it would take to close the gap (small, low-risk):**
- Reword the help line so it's clear the panel appears once there's developer activity — ~30 minutes, no code change; and/or
- Show an empty "Developer details — nothing logged yet" placeholder so it's always visible and matches the help — ~2–4 hours.

No data-model, command-line, or agent changes are needed — the underlying fields already exist.

**One decision for you:** should Developer details be visible to **stakeholders** at all, or stay PM/dev-only? That's the product call that decides whether the fix is just the help wording or also a stakeholder-facing view.
