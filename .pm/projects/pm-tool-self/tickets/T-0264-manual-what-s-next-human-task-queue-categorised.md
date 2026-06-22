---
id: T-0264
title: Manual "What's next" — human task queue (categorised)
type: feature
state: triaged
created: 2026-06-05T22:43:55Z
updated: 2026-06-22T16:13:35Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - "Per-user: each signed-in person has their own private 'what's next' queue keyed to their identity — no one else sees it."
  - "Add an item with a title, optional note, and a category from a fixed set: review code · action · decision · test a feature · review a sprint · other."
  - Tick off (complete), reorder, and delete items; completed items hide by default but are archived and viewable.
  - Surfaced as its own page (/whats-next) plus a compact card on the dashboard that links to it; complements — doesn't replace — the automated dashboard sections.
  - Order + completion persist across sessions/devices (stored per-email under the data repo).
out_of_scope:
  - Shared / team-wide lists — per-user only for now.
  - User-defined custom categories — fixed set + 'other' for now.
code_anchors:
  - path: web/app/page.tsx
    symbol: dashboard card linking to /whats-next
  - path: web/app/whats-next/page.tsx
    symbol: new page
  - path: web/app/_lib/access.ts
    symbol: getEffectiveIdentity (per-user key)
  - path: cli/src/lib/io.ts
    symbol: per-user read/write
  - path: cli/src/types.ts
    symbol: new WhatsNextItem type
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 1
---

## Problem

The dashboard is all automated signals (needs-you-now, agents working, ready-to-close). There's no place for a human to manually jot down and track the things THEY know they need to do.

## What we're building (decided 2026-06-08)

A **per-user** "What's next" queue — each signed-in person's own private list, keyed to their identity:
- Items have a **title**, optional **note**, and a **category** from a fixed set: review code · action · decision · test a feature · review a sprint · other.
- **Add / tick-off / reorder / delete.** Completed items hide by default but are archived and viewable.
- Surfaced as its **own page** (`/whats-next`) plus a compact card on the dashboard that links to it. Complements — doesn't replace — the automated sections.
- **Per-user storage** under the data repo (keyed to email), so it persists across sessions/devices.

## Out of scope

Shared/team lists (per-user only for now); custom categories (fixed set + "other").
