---
id: T-0636
title: Rename the 'ready' ticket state to something clearer (e.g. "Ready for Dev")
type: chore
state: triaged
created: 2026-07-22T05:59:13Z
updated: 2026-07-22T05:59:13Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: build session (stock tickets)
assignee: null
acceptance_criteria:
  - The `ready` state's human-facing label reads unambiguously as 'queued to start' (e.g. 'Ready for Dev') everywhere it surfaces — board column, state dropdowns, dashboards, and any emails.
  - The underlying state id/key stays `ready` — no data migration; transitions/behaviour unchanged.
  - Final wording agreed with the team (Ready for Dev / To Do / Selected / Refined).
out_of_scope: []
code_anchors:
  - path: web/app/
    note: board column + state label rendering (the 'Ready' column / status chips)
  - path: cli/src/
    note: shared ticket-state definitions / display labels
  - path: mcp-server/src/
    note: state-transition tooling that references the state
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - ux
attention: null
version: 1
---

## Problem
The ticket state **`ready`** (pipeline: triaged → ready → in_progress → review → done) is misleading. "Ready" reads like *"ready as in finished / ready to ship"*, when it actually means *"ready to be **started**"* — Definition of Ready: groomed, has acceptance criteria + code anchors, safe to pull into work. It sits **before** the work, but the word implies **after** it.

Surfaced by Zsolt while moving stock tickets through the pipeline.

## What's wanted
Rename the state's **human-facing label** to something that unambiguously means "queued to start" — e.g. **"Ready for Dev"** (other candidates: "To Do", "Selected", "Refined"). Keep the underlying state behaviour; just make the label clear.

## Notes
- Change the label from **one place** (the state definition) so it updates everywhere it surfaces — board column, state dropdowns, dashboards, any emails — not per-view.
- Keep the internal state id (`ready`) stable to avoid a data migration; only the display label changes. Confirm nothing keys off the label text itself.
- Final wording is a quick team call.