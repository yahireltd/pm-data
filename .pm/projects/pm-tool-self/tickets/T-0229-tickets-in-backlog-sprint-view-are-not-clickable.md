---
id: T-0229
title: Tickets in backlog / sprint view are not clickable
type: feature
state: done
priority: p2
created: 2026-06-05T13:57:52Z
updated: 2026-06-05T21:26:26Z
project: pm-tool-self
section: null
parent: null
children: []
order: 62464
reporter: null
assignee: null
acceptance_criteria:
  - "[x] when clicking a ticket in the backlog or sprint area that ticket opens"
out_of_scope: []
code_anchors:
  - path: web/app/_components/ProductBacklog.tsx
  - path: web/app/_components/SprintsList.tsx
  - path: web/app/_components/RichBodyEditor.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

**When doing new ticket you can not click into the text area with the mouse i have to click bold (or any ui button to get the cursor in the text area)**

## Conversation

**2026-06-05 16:53 — you**

now working

***

**2026-06-05 16:53 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.

**2026-06-05 21:26 claude:** **What we did** — Made ticket titles in the Backlog and Sprints views clickable so they open the ticket, and fixed the rich-text boxes so clicking anywhere in them places your cursor (no more having to click a toolbar button first to start typing).

**Why** — You couldn't open a ticket straight from those lists, and starting to type in a new ticket's text needed an awkward extra click.

**What would have happened if we did nothing** — Everyday navigating and writing stayed needlessly fiddly, slowing routine work.

**The benefit** — Faster, more natural navigation and editing for everyone using the tool.
