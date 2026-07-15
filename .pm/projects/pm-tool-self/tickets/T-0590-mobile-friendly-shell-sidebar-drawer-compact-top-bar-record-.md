---
id: T-0590
title: "Mobile-friendly shell: sidebar drawer + compact top bar (record meetings from phone/tablet)"
type: feature
state: review
created: 2026-07-15T14:11:01Z
updated: 2026-07-15T14:14:59Z
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
  - On a phone-width viewport the sidebar is hidden behind a hamburger; opening it slides a drawer over the content, backdrop tap or navigating closes it
  - Desktop (lg+) layout is pixel-identical to today
  - "The top bar fits a phone: hamburger + compact actions, no horizontal page overflow"
  - "A meeting page is usable on a phone: record button reachable, attachments/outcomes readable, no layout breakage"
  - Recording works from a mobile browser (iOS Safari / Android Chrome) end to end
out_of_scope: []
code_anchors:
  - path: web/app/_components/AppShell.tsx
    symbol: responsive shell + drawer (new)
  - path: web/app/layout.tsx
    symbol: shell wiring
  - path: web/app/_components/TopNav.tsx
    symbol: hamburger + compaction
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1411
    started: 2026-07-15T14:11:21Z
    status: completed
    ended: 2026-07-15T14:14:59Z
    summary: "The tool now works on a phone or tablet — which matters because the new Record button is most useful on the device you carry into the meeting. The problem was structural: the side menu was a fixed column that ate three-quarters of a phone's screen with no way to hide it. Now, on small screens the menu tucks away behind a ☰ button in the top bar and slides in over the page when you need it (tapping outside, pressing Escape, or navigating closes it); on a desktop nothing changes at all. The top bar also compacts on phones — the New-ticket button becomes icon-only and your name hides at the narrowest widths so nothing overflows. With the shell fixed, the meeting page (already single-column-friendly) is fully usable on mobile: open the meeting, tap Record, stop, and the recording attaches — then the Mac worker picks it up as usual. The remaining desktop-shaped screens (tickets table, kanban board, phases planner, settings tables) are deliberately a separate phase-2 ticket (T-0591). If we did nothing, in-the-room recording would mean carrying a laptop. Benefit: the record-a-meeting flow works from the device in your pocket."
    test_plan: '1. After deploy, open the tool on your PHONE (or narrow a desktop window below ~1024px): the sidebar is gone; a ☰ button sits top-left. 2. Tap ☰ → the menu slides in over the page; tap the dark backdrop → closes; open it and tap a link → navigates AND closes. 3. Top bar on the phone: no sideways page scroll; New ticket is icon-only; the workflow badges scroll horizontally within the bar. 4. THE HEADLINE TEST: on your phone/tablet, open a meeting → tap "Record meeting" → allow the mic → speak → "Stop & attach" → the recording appears in Attachments. Run transcribe-meetings on the Mac → transcript lands. (iOS Safari and Android Chrome both.) 5. Desktop regression: at full width everything is pixel-identical — fixed sidebar, full top bar, no hamburger. 6. Esc closes the drawer; work-mode toggle/preview banner unaffected. Phase 2 (tables/kanban/planner on mobile) is T-0591.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - mobile
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T14:14:59Z
version: 4
---

## Problem

The meeting Record button (T-0588) is most useful on a phone/tablet in the room — but the tool is unusable on small screens: the sidebar is a fixed 288px column with no mobile handling (~75% of a phone's width), and the top bar assumes desktop width. The rest of the app is Tailwind with fluid widths, so the shell is the one big blocker.

## Design (phase 1 of the mobile pass)

- App shell: below lg the sidebar becomes an off-canvas drawer (hamburger in the top bar, backdrop tap + navigation close it); desktop unchanged.
- Top bar compacts on small screens: hamburger appears, New-ticket goes icon-only, the signed-in name hides on the narrowest widths, workflow badges keep their existing horizontal scroll.
- Meeting page + Record flow verified usable on a phone-width viewport.

Phase 2 (separate ticket): the wide desktop surfaces — tickets table, kanban, phases planner, settings tables.

## Conversation

**2026-07-15 14:14 claude-code:** Run run-20260715-1411 completed — The tool now works on a phone or tablet — which matters because the new Record button is most useful on the device you carry into the meeting. The problem was structural: the side menu was a fixed column that ate three-quarters of a phone's screen with no way to hide it. Now, on small screens the menu tucks away behind a ☰ button in the top bar and slides in over the page when you need it (tapping outside, pressing Escape, or navigating closes it); on a desktop nothing changes at all. The top bar also compacts on phones — the New-ticket button becomes icon-only and your name hides at the narrowest widths so nothing overflows. With the shell fixed, the meeting page (already single-column-friendly) is fully usable on mobile: open the meeting, tap Record, stop, and the recording attaches — then the Mac worker picks it up as usual. The remaining desktop-shaped screens (tickets table, kanban board, phases planner, settings tables) are deliberately a separate phase-2 ticket (T-0591). If we did nothing, in-the-room recording would mean carrying a laptop. Benefit: the record-a-meeting flow works from the device in your pocket.
