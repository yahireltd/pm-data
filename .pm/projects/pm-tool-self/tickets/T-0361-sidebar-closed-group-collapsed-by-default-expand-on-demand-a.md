---
id: T-0361
title: "Sidebar: Closed group collapsed by default (expand on demand; auto-expand when you're inside a closed project)"
type: feature
state: done
created: 2026-06-12T01:28:49Z
updated: 2026-06-12T01:46:45Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] The Closed sidebar group renders collapsed by default, showing a count; clicking the header expands/collapses it"
  - "[x] When the route is inside a closed project, the group is expanded automatically so the active project's sub-nav never vanishes"
  - "[x] Systems/Initiatives groups unchanged"
out_of_scope: []
code_anchors:
  - path: web/app/_components/SidebarNav.tsx
    symbol: closed group block + renderProject
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0129
    model: claude-fable-5
    started: 2026-06-12T01:29:10Z
    status: completed
    ended: 2026-06-12T01:29:58Z
    summary: |-
      **What we did**
      The "Closed" group in the left menu now starts collapsed — a single line showing "Closed" and a count. Click it to expand; it opens itself automatically if you're actually inside one of the closed projects, so the menu never disappears from under you.

      **Why we did it**
      After the project tidy-up, several retired projects landed in the Closed group, and even dimmed they took up space on every screen.

      **If we did nothing**
      The list of retired projects would keep growing down the sidebar forever — history crowding out current work.

      **The benefit**
      The menu shows only what you run and what you're delivering; the past is one click away instead of always in view.
    test_plan: |-
      After `pm-deploy` (commit 6793ee4 — also carries the ticket Branch "Load branches" picker from earlier) and a refresh:
      1. Sidebar shows "CLOSED · 3" as a single collapsed line with a chevron — the three retired projects are hidden.
      2. Click it → expands (dimmed rows); click again → collapses.
      3. Open a closed project (e.g. /projects/yasite) → the group is auto-expanded and the project's sub-nav shows; while on that page the group can't be collapsed (by design — the active project must stay visible).
      4. Systems / Initiatives groups unchanged.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - taxonomy
  - ux
attention: null
version: 9
---

## Problem

After the T-0353 consolidation the Closed group holds several retired projects — dimming isn't enough; Austin wants it out of the way entirely unless asked for.

## Design

Collapsible header ("Closed · N") with a chevron, collapsed by default; clicking toggles. Auto-expands while the current route is a closed project so the expanded sub-nav can't disappear from under you.

## Conversation

**2026-06-12 01:29 claude-code:** Run run-20260612-0129 completed — **What we did**
The "Closed" group in the left menu now starts collapsed — a single line showing "Closed" and a count. Click it to expand; it opens itself automatically if you're actually inside one of the closed projects, so the menu never disappears from under you.

**Why we did it**
After the project tidy-up, several retired projects landed in the Closed group, and even dimmed they took up space on every screen.

**If we did nothing**
The list of retired projects would keep growing down the sidebar forever — history crowding out current work.

**The benefit**
The menu shows only what you run and what you're delivering; the past is one click away instead of always in view.

---

**2026-06-12 01:46 — you**

closed not visible
