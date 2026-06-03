---
id: T-0176
title: "Force close-the-loop: finished agent runs + human closes must transition state and record an outcome"
type: feature
state: triaged
created: 2026-06-03T17:41:05Z
updated: 2026-06-03T17:41:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - "pm_complete_run with status=completed moves the ticket in_progress→review, sets attention(needed_by:human), and records the run summary as a comment in the ticket's ## Conversation"
  - "CLI `pm record-run` mirrors complete-run: completed→review+attention+recorded summary, abandoned→ready+clear assignee; the interactive prompt no longer defaults to an open-ended in_progress run"
  - A warn-level lint rule flags in_progress tickets whose newest agent_run is completed/ended (STALECLOSE001) and in_progress tickets with no progress for > N hours (STALEPROG001), registered like the at_risk rule; the progress[] field is formalized in agent-run.schema.json
  - The Today dashboard shows a 'Dropped / stalled work' section (shipped-unclosed + stale in_progress); finished-run tickets no longer appear in 'Agents working now'; board card + WhatsNext show a 'shipped but not closed' indicator
  - "No human path can move a ticket to done (CLI pm state, Kanban drag, StateSelector, WhatsNext approve) without a closing note; the note is recorded to ## Conversation and the attention flag is cleared on done"
  - Existing CLI/linter tests pass, the new linter rule has tests, and web typecheck + build are clean
out_of_scope:
  - Git/merged-commit reconciliation for shipped-but-never-run tickets (T-0162 class)
  - Multi-tenant / per-user scoping
code_anchors:
  - path: mcp-server/src/tools/complete-run.ts
    note: A1 — completed→review+attention+closing comment
  - path: cli/src/commands/record-run.ts
    note: A2 — mirror completed/abandoned branches
  - path: linter/src/rules/in-progress-staleness.ts
    note: B1 — new STALECLOSE001/STALEPROG001 rules
  - path: web/app/_lib/health.ts
    note: B2 — derive shipped-unclosed/stale signals
  - path: web/app/page.tsx
    note: B2 — Dropped/stalled work dashboard section
  - path: cli/src/lib/state-machine.ts
    note: D1 — requiresClosingNote(from,to) shared gate
  - path: web/app/_actions/tickets.ts
    note: D1 — setTicketState closing-note gate + auto-clear attention on done
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - lifecycle
attention: null
---

## Problem

Work repeatedly gets stranded: an agent finishes (e.g. SSO / T-0172) but the ticket sits in `in_progress` forever, and shipped tickets (T-0162) stay open. Root cause found by audit: `pm_complete_run` with `status="completed"` is a deliberate no-op on the ticket (`complete-run.ts:96-99` — "stay in in_progress, human moves it"). It sets no attention flag, so a finished ticket looks identical to live work everywhere (Agents-working-now, /watch) and lands in zero "needs you" queues. Over MCP an agent **cannot** reach `review`/`done` itself, so `completed` is the natural word but routes straight into the silent state. There is also no ticket-level staleness signal anywhere, and humans can mark `done` with no recorded reason.

Reminders won't fix this — it's a tool default. Per ADR-003 the gate must bite.

## Approach (decided with Austin: full sweep + human done-gate; completed → auto-review)

- **A1 (MCP):** `pm_complete_run` `completed` → ticket `in_progress`→`review`, set `attention{needed_by:human}`, and record the run summary as a closing comment in `## Conversation`.
- **A2 (CLI):** `pm record-run` mirrors A1 (add `completed`/`abandoned` branches; stop defaulting the interactive prompt to an open-ended `in_progress` run).
- **B1 (linter):** warn rules — `STALECLOSE001` (in_progress but newest run is completed/ended) and `STALEPROG001` (in_progress, no progress > N h); formalize the runtime `progress[]` field in `agent-run.schema.json`.
- **B2 (web):** derive shipped-unclosed/stale signals in `_lib/health.ts`; add a "Dropped / stalled work" section to the Today dashboard; split finished-run tickets out of "Agents working now"; board-card + WhatsNext "shipped but not closed" indicator.
- **D1 (human done-gate):** require a closing note before any `*→done` (CLI `pm state`, Kanban drag, StateSelector, WhatsNext approve); record it to `## Conversation`; auto-clear attention on done.

## Out of scope (deferred)

- Git/merged-commit reconciliation for the "shipped to prod but never run through the loop" class (T-0162) — separate L-effort ticket.
