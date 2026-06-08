---
id: T-0278
title: sticky menu on the user guide and agents md guidance to keep it updated
type: feature
state: review
priority: p2
created: 2026-06-06T01:06:28Z
updated: 2026-06-08T14:17:59Z
project: pm-tool-self
section: null
parent: null
children: []
order: 90112
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - sticky menu on userguide that is visible when we scroll down
out_of_scope: []
code_anchors:
  - path: web/app/help/page.tsx
    symbol: HelpPage
  - path: AGENTS.md
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1416
    model: claude-opus-4-8
    started: 2026-06-08T14:16:50Z
    status: completed
    ended: 2026-06-08T14:17:59Z
    summary: The user guide's table of contents now stays put as you read. Before, the contents was a block at the very top of the guide, so once you scrolled down into a section it disappeared and you had to scroll all the way back up to jump elsewhere. On normal-width screens the contents is now a menu pinned to the side that stays visible the whole time you scroll, so you can hop between topics from anywhere in the guide. On small screens it still sits at the top as before. We also tightened the internal rule book so that whenever someone changes a screen, updating that screen's help is part of the same change — the browsable guide is built from those per-screen help files, so this keeps the guide from drifting out of date.
    test_plan: |-
      1. Open /help (the User guide).
      2. On a wide (desktop) window, scroll down through the guide → the "Contents" menu stays pinned on the left and visible the whole way down; clicking a link still jumps to that section (with the sticky top bar offset respected).
      3. Narrow the window (mobile width) → the Contents collapses back to the top and scrolls away normally; layout doesn't break.
      4. Long contents: if the menu is taller than the screen it scrolls independently rather than being cut off.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T14:17:59Z
---

## Problem

_What's wrong / what's needed?_

## Context

## Design notes

## Conversation

**2026-06-08 14:17 claude:** Run run-20260608-1416 completed — The user guide's table of contents now stays put as you read. Before, the contents was a block at the very top of the guide, so once you scrolled down into a section it disappeared and you had to scroll all the way back up to jump elsewhere. On normal-width screens the contents is now a menu pinned to the side that stays visible the whole time you scroll, so you can hop between topics from anywhere in the guide. On small screens it still sits at the top as before. We also tightened the internal rule book so that whenever someone changes a screen, updating that screen's help is part of the same change — the browsable guide is built from those per-screen help files, so this keeps the guide from drifting out of date.
