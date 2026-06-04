---
id: ADR-027
slug: optimistic-ui-as-the-cross-cutting-convention-for-instant-in
title: Optimistic UI as the cross-cutting convention for instant in-place mutations
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0196
  - T-0193
  - T-0194
---

## Context
Small, frequent edits — toggling an acceptance-criteria checkbox, ticking the review verify checklist, dragging a card between board columns — felt laggy when each waited for a server round-trip, and naive useEffect resync of server state during in-flight writes caused flicker and lost edits. The alternatives were to accept the round-trip latency, or to hand-roll ad hoc local state per widget.

## Decision
Standardize on React's useOptimistic for these in-place mutations: the UI flips instantly, the server action runs in the background, and the optimistic state is reverted on failure. The pattern is applied consistently (acceptance-criteria checkboxes T-0196, the ReviewBanner verify checklist, Kanban drag-drop) and explicitly drops the useEffect server->local resync that fought the optimistic update.

## Consequences
Optimistic state can briefly diverge from the server, so every such action needs a correct revert-on-failure path and care not to resync mid-flight — a recurring source of subtle bugs if a new widget copies the pattern carelessly. Accepting that complexity buys instant, native-feeling interaction for the highest-frequency edits and a single agreed approach instead of per-component improvisation.