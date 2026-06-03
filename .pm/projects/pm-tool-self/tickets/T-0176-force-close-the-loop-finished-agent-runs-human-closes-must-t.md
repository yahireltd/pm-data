---
id: T-0176
title: "Force close-the-loop: finished agent runs + human closes must transition state and record an outcome"
type: feature
state: done
created: 2026-06-03T17:41:05Z
updated: 2026-06-03T18:18:17Z
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
  - "[x] pm_complete_run with status=completed moves the ticket in_progress→review, sets attention(needed_by:human), and records the run summary as a comment in the ticket's ## Conversation"
  - "[x] CLI `pm record-run` mirrors complete-run: completed→review+attention+recorded summary, abandoned→ready+clear assignee; the interactive prompt no longer defaults to an open-ended in_progress run"
  - "[x] A warn-level lint rule flags in_progress tickets whose newest agent_run is completed/ended (STALECLOSE001) and in_progress tickets with no progress for > N hours (STALEPROG001), registered like the at_risk rule; the progress[] field is formalized in agent-run.schema.json"
  - "[x] The Today dashboard shows a 'Dropped / stalled work' section (shipped-unclosed + stale in_progress); finished-run tickets no longer appear in 'Agents working now'; board card + WhatsNext show a 'shipped but not closed' indicator"
  - "[x] No human path can move a ticket to done (CLI pm state, Kanban drag, StateSelector, WhatsNext approve) without a closing note; the note is recorded to ## Conversation and the attention flag is cleared on done"
  - "[x] Existing CLI/linter tests pass, the new linter rule has tests, and web typecheck + build are clean"
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
agent_runs:
  - id: run-20260603-1808
    model: claude-code
    started: 2026-06-03T18:08:03Z
    status: needs_review
    summary: Implemented the close-the-loop forcing function — A1 (pm_complete_run completed→review+attention+closing comment), A2 (CLI record-run mirror), D1 (human done-gate via requiresClosingNote), B1 (STALECLOSE001/STALEPROG001 lint), B2 (Dropped/stalled-work dashboard surface) — in commit ba49c1c (already live), plus two review follow-ups in a6ec3fb (styled closing-note modal + pm_mark_ready MCP tool) awaiting deploy.
    ended: 2026-06-03T18:16:25Z
    test_plan: |-
      After `git push origin master` + `sudo pm-deploy`:
      1. **Agent close** — complete any run with `status=completed`: the ticket auto-moves to **review** with a closing comment in `## Conversation` (no more silent `in_progress`).
      2. **Human done-gate** — drag a card to Done, or use "Approve & mark done": the **styled** closing-note modal appears (not the browser prompt); an empty note is rejected; the note is written to `## Conversation`; the attention flag clears on done.
      3. **Dashboard** — an `in_progress` ticket whose latest run finished shows under **Dropped / stalled work**, not "Agents working now".
      4. **Linter** — `pm-lint` warns `STALECLOSE001` / `STALEPROG001` on a stranded `in_progress` ticket.
      5. **MCP** — `pm_mark_ready` on a triaged ticket (with acceptance criteria + code anchors) moves it to `ready`.
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

**2026-06-03 18:00 claude:** **Implementation complete & committed locally** (commit `ba49c1c` on master) — awaiting push + `sudo pm-deploy`, then review & close.

**What shipped** (full sweep + human done-gate; `completed` → auto-review, per the decision):

- **A1 (MCP)** — `pm_complete_run` `completed` now moves the ticket `in_progress`→`review`, sets a human attention flag, and records the run summary as a closing comment in `## Conversation`. Agents still cannot reach `done`.
- **A2 (CLI)** — `pm record-run` mirrors A1 (`completed`→review+attention+comment; `abandoned`→ready + cleared assignee).
- **D1 (done-gate)** — no human path can mark a ticket `done` without a closing note: CLI `pm state --note`, Kanban drag, StateSelector, WhatsNext approve, ReviewBanner. The note is written to `## Conversation`; attention is cleared on done. Enforced once in the shared state machine via `requiresClosingNote()`.
- **B1 (linter)** — warn-level `STALECLOSE001` (in_progress with a finished latest run) + `STALEPROG001` (open run gone quiet > 24h); `progress[]` formalized in the agent-run schema; tests added.
- **B2 (web)** — a "Dropped / stalled work" section on Today, finished-run tickets split out of "Agents working now", plus a board-card + WhatsNext "shipped but not closed" indicator.

**Verification**: linter 19/19 + typecheck ✓ · cli/mcp typecheck ✓ · web typecheck + full `next build` ✓. (The CLI runtime smoke tests can't run in this checkout — they copy the now-removed local `.pm/`; pre-existing, unrelated to this change. Worth a follow-up to bundle a fixture seed.)

**Deferred** (separate ticket): git/merged-commit reconciliation for the "shipped to prod but never run through the loop" class — i.e. T-0162.

**Dogfood gap found**: an agent driving via MCP can't move a ticket `triaged`→`ready` (no MCP tool), so I couldn't claim/run T-0176 through the loop itself. Recorded here as a comment + review flag instead.

**Hand-off**: push master → `sudo pm-deploy` → then close T-0176 (and the already-shipped T-0172 / T-0162) via the new done-gate, which will prompt you for a closing note.

**2026-06-03 18:16 claude-code:** **Round 2 committed** (`a6ec3fb`) — two refinements from your feedback, awaiting push + `sudo pm-deploy`:

1. **Styled closing-note modal** — the done-gate note (and the send-back reason + claim-name prompts) no longer use the browser's native `window.prompt` (that ugly dialog in your screenshot). New root-mounted `TextPromptProvider` + `useTextPrompt()`, styled like the existing `ForceAdvanceDialog`. Wired into StateSelector, KanbanBoard, WhatsNext, ReviewBanner.
2. **`pm_mark_ready` MCP tool** — moves a `triaged` ticket → `ready` over MCP (enforcing Definition of Ready); the counterpart to the UI's "Queue for agent". Fixes the dogfood gap where an agent on MCP couldn't queue a ticket to then claim it.

Verified: mcp typecheck ✓ · web typecheck + full `next build` ✓.

Note: the native-prompt screenshot was commit 1 (`ba49c1c`, already live) working — commit 2 styles it. Completing this run as **needs_review**: push + `sudo pm-deploy`, then approve to close (the close will use the new styled modal once deployed).

## Conversation

**2026-06-03 18:18 — you**

Checked and approved
