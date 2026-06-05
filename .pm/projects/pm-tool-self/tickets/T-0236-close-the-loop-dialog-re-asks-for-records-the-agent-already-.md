---
id: T-0236
title: Close-the-loop dialog re-asks for records the agent already recorded
type: feature
state: triaged
created: 2026-06-05T16:08:36Z
updated: 2026-06-05T16:08:36Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Closing a ticket whose latest agent run has a records attestation opens the dialog pre-filled with those answers (docs/tech-session/status toggles + the TS-id)
  - The person can confirm in one click, or change any answer before confirming
  - A ticket with no prior attestation still opens blank, as today
  - Server-side records validation is unchanged
out_of_scope: []
code_anchors:
  - path: web/app/_components/RecordsGateProvider.tsx
  - path: web/app/_actions/tickets.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - meta
attention: null
---

## Problem

When an agent finishes a ticket it records what it did and what it updated (docs, a tech-session, a status note) — that's the "close the loop" attestation. But when a person then goes to close the ticket, the close dialog opens **completely blank** and makes them answer the same three questions again by hand. The agent's answers are already on file, but the dialog ignores them.

So the loop gets closed twice, and the second time lands on the human as data-entry rather than a quick confirmation — exactly backwards from the intent.

## What we want

When closing a ticket whose agent run already left a records attestation, the dialog should open **pre-filled with the agent's answers** (the docs / tech-session / status-note choices, and the tech-session id). The person just glances and confirms — or changes anything they disagree with. The human still has the final say (that gate is deliberate), it just stops being a re-typing exercise.

## Why it matters

The whole point of the close-the-loop step is to make sure nothing the work should have recorded gets forgotten. If the person has to re-enter what the agent already recorded, it's busywork they'll start rubber-stamping — which defeats the check. Showing them the agent's record to confirm keeps the check meaningful and quick.

## Out of scope
- Changing the server-side validation (unchanged — it still re-checks on close).
- Removing the human confirmation step (we keep it; we just pre-fill it).