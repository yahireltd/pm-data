---
id: T-0236
title: Close-the-loop dialog re-asks for records the agent already recorded
type: feature
state: review
created: 2026-06-05T16:08:36Z
updated: 2026-06-05T16:36:37Z
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
    ended: 2026-06-05T16:36:37Z
    summary: 'When someone closes a finished ticket, the close dialog now shows the answers the agent already recorded, ready to confirm — instead of a blank form to re-fill. After Austin pointed out that an "all not-applicable" record still looked blank, we made it unmistakable: a "pre-filled from the agent" tag, a tick on each pre-selected answer, and the unused option dimmed, so it clearly reads as "already answered, just confirm." We also wrote down both ends of this: the agent guide now says agents must set these records accurately because the person closing simply confirms them, and the in-app developer notes explain the pre-filled close. Net effect: the loop is recorded once by the agent and confirmed in one glance by the person — no double data-entry.'
    test_plan: "Close a reviewed ticket: the dialog opens with the agent's answers pre-selected, a \"pre-filled from the agent\" tag at the top, a tick on each chosen answer, and the other option dimmed — Confirm is ready immediately. Try one whose answers are all \"n/a\" (e.g. this ticket): it should clearly look answered, not blank. Try one with a tech-session id (e.g. T-0226): the TS-id shows filled in. Change an answer before confirming to check edits still work. A ticket with no agent attestation still opens blank."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §1 notes the human close confirms the agent's attestation; dev wiki Conventions documents the pre-filled close.
labels:
  - dogfood
  - meta
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T16:36:37Z
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

---

**2026-06-05 16:32 — you**

done
