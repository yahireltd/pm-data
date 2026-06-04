---
id: ADR-022
slug: force-the-close-the-loop-every-finished-run-and-every-human-
title: "Force the close-the-loop: every finished run and every human close must transition state and record an outcome"
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0176
---

## Context
The failure mode being designed against: an agent finishes work but the ticket silently rests in `in_progress`, so shipped work gets dropped on the floor and nobody knows it's waiting. The lax option is to let pm_complete_run just stamp the run's status and leave the ticket state to the caller's discretion, and let humans close tickets with no recorded reason.

## Decision
pm_complete_run drives the ticket state machine deterministically: `completed`/`needs_review` move the ticket to `review` with attention.needed_by=human and record the run summary as a closing ## Conversation comment; `failed`/`abandoned` bounce back to `ready`, clear the assignee, and stamp an attention reason. Agents can NEVER reach `done` — only a human confirms and closes, and that close requires a recorded closing note (requiresClosingNote gate, consulted by both CLI and web so it can't be bypassed by surface). Meeting outcomes get the parallel treatment via pm_record_outcome (optionally promoting to a linked follow-up ticket).

## Consequences
Agents lose the ability to self-close, and finishing a run is never a quiet no-op — it always changes state and flags a human, which adds review-queue traffic and forces the closing-note step. The trade we accepted: more mandatory transitions and human handoffs in exchange for the guarantee that no completed work, and no close, ever happens without a recorded WHY and an explicit state change.