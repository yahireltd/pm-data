---
id: T-0397
title: Kill list — remove pm-tool parts we're not actually using
type: feature
state: triaged
priority: p2
created: 2026-06-17T11:19:03Z
updated: 2026-06-22T14:03:44Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 109568
reporter: null
assignee: null
acceptance_criteria:
  - A reviewed cut-list exists — each candidate with usage evidence and a hide-vs-remove call
  - Austin signs off the list before any removal
  - Agreed items are hidden/removed, each tracked
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## Problem

Trim pm-tool of features we're genuinely not using, to cut clutter and maintenance load. A deliberate "kill list" pass.

## Context

Raised 2026-06-17, clarified 2026-06-22 (Austin): this is about *genuinely-unused* parts only. The **time plan is NOT a kill target** — we want to keep it but hide it for now while it's reworked at sprint level (see **T-0398**).

## Design notes

- First action: produce a **candidate cut-list with usage evidence** for each item (is any web/CLI/MCP path exercising it? last touched? referenced anywhere?) — *before* removing anything.
- Specific kill targets still **TBD by Austin** — the unused features weren't enumerated yet.
- Per item: **hide first** (low-risk, reversible) → remove once confirmed dead.