---
id: T-0590
title: "Mobile-friendly shell: sidebar drawer + compact top bar (record meetings from phone/tablet)"
type: feature
state: in_progress
created: 2026-07-15T14:11:01Z
updated: 2026-07-15T14:11:21Z
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
    status: in_progress
labels:
  - ux
  - mobile
attention: null
version: 3
---

## Problem

The meeting Record button (T-0588) is most useful on a phone/tablet in the room — but the tool is unusable on small screens: the sidebar is a fixed 288px column with no mobile handling (~75% of a phone's width), and the top bar assumes desktop width. The rest of the app is Tailwind with fluid widths, so the shell is the one big blocker.

## Design (phase 1 of the mobile pass)

- App shell: below lg the sidebar becomes an off-canvas drawer (hamburger in the top bar, backdrop tap + navigation close it); desktop unchanged.
- Top bar compacts on small screens: hamburger appears, New-ticket goes icon-only, the signed-in name hides on the narrowest widths, workflow badges keep their existing horizontal scroll.
- Meeting page + Record flow verified usable on a phone-width viewport.

Phase 2 (separate ticket): the wide desktop surfaces — tickets table, kanban, phases planner, settings tables.
