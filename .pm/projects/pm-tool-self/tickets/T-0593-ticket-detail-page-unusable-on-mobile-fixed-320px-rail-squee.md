---
id: T-0593
title: "Mobile priority surfaces: ticket view, meeting view, top menu"
type: bug
state: review
created: 2026-07-15T14:37:56Z
updated: 2026-07-15T14:43:17Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Ticket page on a phone: one readable column, meta rail stacked below content full-width, no horizontal scroll, no one-word-per-line"
  - "Meeting page on a phone: the header controls (state/kind/repeats + action buttons) wrap instead of overflowing; content stacks (already single-column)"
  - "Top menu on a phone: all workflow items visible WITHOUT sideways scrolling (icon + count badges, labels return on wider screens)"
  - Desktop (lg+) unchanged on all three surfaces
out_of_scope: []
code_anchors:
  - path: web/app/tickets/[id]/page.tsx
    symbol: row container (l.205) + w-80 aside (l.488)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1438
    started: 2026-07-15T14:38:36Z
    status: completed
    ended: 2026-07-15T14:43:17Z
    summary: "The three screens you use most from a phone now fit a phone. The ticket page was the worst: its layout reserved a fixed 320-pixel side panel no matter how small the screen, leaving the actual ticket about 70 pixels — the one-word-per-line smear in your screenshot. On phones the side panel (assignee, files, reporter, project…) now sits below the content at full width, and the page reads as one clean column; on a desktop nothing changes. The meeting page's header — the row of state/kind/repeat selectors and action buttons — now wraps onto extra lines instead of spilling off-screen. And the top menu no longer hides most destinations behind a sideways scrollbar: on phones each item shows as its coloured icon with its red count badge (labels come back on wider screens), so all seven fit at once. If we'd left these, phone use would stay limited to the meeting-record trick. Benefit: reviewing tickets and running meetings from your phone is now genuinely workable."
    test_plan: "1. After deploy, on your PHONE open the same ticket as your screenshot: one readable column, the meta panel (assignee/files/reporter/project) below the content, no sideways scrolling, normal word wrapping. 2. Open a meeting (e.g. M-010): the header's selectors and buttons wrap neatly onto rows; recording/attachments/outcomes all reachable. 3. Look at the top bar: all seven workflow icons with their count badges visible at once — no scrollbar; tapping each goes to the right page (long-press/hover shows the name). 4. On DESKTOP: ticket page identical to before (side rail on the right), meeting header one row as before, top menu shows full labels. 5. Sanity: /tickets list and kanban are still awaiting the phase-2 pass (T-0591) — not covered here."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - mobile
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T14:43:17Z
version: 5
---

## Problem

After the mobile shell (T-0590), the ticket detail page is still broken on a phone: the layout is a flex ROW of main content + a fixed `w-80` (320px) right rail that never stacks — on a 390px screen the main column gets ~70px and renders one word per line (Austin's screenshot, 15 Jul). The ticket page is the single most-opened page from a phone (review queue), so it can't wait for the general phase-2 pass (T-0591).

## Fix

Stack below lg: container `flex-col lg:flex-row`; the rail becomes full-width below the content (border-top instead of border-left, no forced min-height); slightly tighter mobile padding. Desktop unchanged.

## Conversation

**2026-07-15 14:43 claude-code:** Run run-20260715-1438 completed — The three screens you use most from a phone now fit a phone. The ticket page was the worst: its layout reserved a fixed 320-pixel side panel no matter how small the screen, leaving the actual ticket about 70 pixels — the one-word-per-line smear in your screenshot. On phones the side panel (assignee, files, reporter, project…) now sits below the content at full width, and the page reads as one clean column; on a desktop nothing changes. The meeting page's header — the row of state/kind/repeat selectors and action buttons — now wraps onto extra lines instead of spilling off-screen. And the top menu no longer hides most destinations behind a sideways scrollbar: on phones each item shows as its coloured icon with its red count badge (labels come back on wider screens), so all seven fit at once. If we'd left these, phone use would stay limited to the meeting-record trick. Benefit: reviewing tickets and running meetings from your phone is now genuinely workable.
