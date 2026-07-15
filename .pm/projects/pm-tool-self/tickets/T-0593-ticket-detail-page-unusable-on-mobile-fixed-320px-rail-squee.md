---
id: T-0593
title: "Mobile priority surfaces: ticket view, meeting view, top menu"
type: bug
state: done
created: 2026-07-15T14:37:56Z
updated: 2026-07-15T15:30:00Z
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
  - "[x] Ticket page on a phone: one readable column, meta rail stacked below content full-width, no horizontal scroll, no one-word-per-line"
  - "[x] Meeting page on a phone: the header controls (state/kind/repeats + action buttons) wrap instead of overflowing; content stacks (already single-column)"
  - "[x] Top menu on a phone: all workflow items visible WITHOUT sideways scrolling (icon + count badges, labels return on wider screens)"
  - "[x] Desktop (lg+) unchanged on all three surfaces"
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
    ended: 2026-07-15T15:00:06Z
    summary: "The phone screens you use most now genuinely fit a phone — including round two on the top menu. The ticket page's fixed side panel (which squeezed content to one word per line) now stacks below the content; the meeting header's selectors and buttons wrap instead of spilling off-screen; and the top menu was rebuilt for mobile as TWO rows: a slim first row (menu button, New ticket, your account) and a second row where the workflow icons get the whole width of the screen, spread out evenly — every destination and its red count badge visible at once, no sideways scrolling at all. The Work-mode toggle no longer appears on phones — it was taking a third of the bar for something rarely flipped on the go (it's still on desktop). Desktop layouts are unchanged everywhere. Benefit: reviewing tickets and running meetings from your phone works without fighting the interface."
    test_plan: "1. After deploy, on your phone: the top of every page shows TWO bars — first: ☰, New-ticket (+), your avatar and sign-out; second: all seven workflow icons with badges spread across the full width. NO horizontal scrolling anywhere in the header, and NO Work-mode pill. 2. Tap each icon in the second bar — right pages open; long-press shows the name. 3. Re-open the ticket from your earlier screenshot: single readable column, meta panel below content. 4. Open M-010: header controls wrap neatly; record/outcomes/transcript all usable. 5. Desktop: single-row header exactly as before, Work mode present, full labels. 6. Still pending in T-0591: tickets LIST, kanban, phases planner, settings tables."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - mobile
  - dogfood-finding
attention: null
version: 11
---

## Problem

After the mobile shell (T-0590), the ticket detail page is still broken on a phone: the layout is a flex ROW of main content + a fixed `w-80` (320px) right rail that never stacks — on a 390px screen the main column gets ~70px and renders one word per line (Austin's screenshot, 15 Jul). The ticket page is the single most-opened page from a phone (review queue), so it can't wait for the general phase-2 pass (T-0591).

## Fix

Stack below lg: container `flex-col lg:flex-row`; the rail becomes full-width below the content (border-top instead of border-left, no forced min-height); slightly tighter mobile padding. Desktop unchanged.

## Conversation

**2026-07-15 14:43 claude-code:** Run run-20260715-1438 completed — The three screens you use most from a phone now fit a phone. The ticket page was the worst: its layout reserved a fixed 320-pixel side panel no matter how small the screen, leaving the actual ticket about 70 pixels — the one-word-per-line smear in your screenshot. On phones the side panel (assignee, files, reporter, project…) now sits below the content at full width, and the page reads as one clean column; on a desktop nothing changes. The meeting page's header — the row of state/kind/repeat selectors and action buttons — now wraps onto extra lines instead of spilling off-screen. And the top menu no longer hides most destinations behind a sideways scrollbar: on phones each item shows as its coloured icon with its red count badge (labels come back on wider screens), so all seven fit at once. If we'd left these, phone use would stay limited to the meeting-record trick. Benefit: reviewing tickets and running meetings from your phone is now genuinely workable.

---

**2026-07-15 15:30 — you**

Done
