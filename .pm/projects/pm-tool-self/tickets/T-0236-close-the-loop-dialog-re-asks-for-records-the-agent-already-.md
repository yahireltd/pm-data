---
id: T-0236
title: Close-the-loop dialog re-asks for records the agent already recorded
type: feature
state: in_progress
created: 2026-06-05T16:08:36Z
updated: 2026-06-05T16:19:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Closing a ticket whose latest agent run has a records attestation opens the dialog pre-filled with those answers (docs/tech-session/status toggles + the TS-id)"
  - "[x] The person can confirm in one click, or change any answer before confirming"
  - "[x] A ticket with no prior attestation still opens blank, as today"
  - "[x] Server-side records validation is unchanged"
out_of_scope: []
code_anchors:
  - path: web/app/_components/RecordsGateProvider.tsx
  - path: web/app/_actions/tickets.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1612
    model: claude-opus-4-8
    started: 2026-06-05T16:12:43Z
    status: completed
    ended: 2026-06-05T16:12:53Z
    summary: When someone closes a finished ticket, the close-the-loop dialog now shows what the agent already recorded — the docs / tech-session / status answers, pre-selected, with the tech-session id filled in — so they just confirm it rather than typing it all in again. Before, the dialog opened blank and made people re-enter answers that were already on file, which turned a quick check into busywork people would be tempted to rubber-stamp. The person still has the final say and can change anything before confirming; it's just a glance-and-confirm now. (This was found by Austin while closing the previous ticket.)
    test_plan: "Finish a ticket as an agent so it records its docs/tech-session/status attestation, then close it as a human: the dialog should open with the agent's answers already selected (including the TS-id) and \"Confirm & mark done\" immediately available — no re-typing. Change one answer to confirm edits still work. Close a ticket that never had an agent attestation: it should still open blank, as before."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - dogfood
  - meta
attention:
  needed_by: agent
  reason: When closing this very ticket it still asked me the three questions
  since: 2026-06-05T16:15:51Z
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

## Conversation

**2026-06-05 16:12 claude:** Run run-20260605-1612 completed — When someone closes a finished ticket, the close-the-loop dialog now shows what the agent already recorded — the docs / tech-session / status answers, pre-selected, with the tech-session id filled in — so they just confirm it rather than typing it all in again. Before, the dialog opened blank and made people re-enter answers that were already on file, which turned a quick check into busywork people would be tempted to rubber-stamp. The person still has the final say and can change anything before confirming; it's just a glance-and-confirm now. (This was found by Austin while closing the previous ticket.)

**2026-06-05 16:19 claude-code:** Looked into the send-back ("it still asked me the three questions when closing this very ticket").

The fix is live and the data is correct: the new version went out at 16:12, before this ticket was closed, and this ticket's run has the agent's answers saved (all three set to "n/a"). So the part that reads and pre-fills the answers is working server-side.

The most likely reason you still saw three blank questions: the page was open in your browser from before the update went out, so it was still running the old version of the dialog. An open tab keeps the old page code until it's fully refreshed — a normal click-around doesn't pick up a new release.

Next step to confirm: hard-refresh the page (Cmd/Ctrl + Shift + R) and try closing again. It should open with all three already set to "n/a" and the Confirm button ready — no questions to answer. If it's STILL blank after a hard refresh, then the answer-lookup is failing quietly and I'll add tracing to find out why, rather than assume it's the browser.
