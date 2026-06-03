---
id: TS-005
slug: inbound-email-tickets-zendesk-style-two-way-inbox
title: Inbound email → tickets (Zendesk-style two-way inbox)
project: pm-tool-self
created: 2026-06-03T22:23:11Z
updated: 2026-06-03T22:23:12Z
decisions:
  - "Thread replies by conversationId AND the [T-NNNN] subject tag: a forward starts a NEW Graph conversation, so conversationId-only matching missed agent replies and created duplicates (8eccba4)."
  - "Idempotent intake: track intake_message_ids[] per ticket; a re-poll of an already-ingested message is a no-op (404b645)."
  - "Clean inbound text: strip quoted reply chains + signatures (Gmail/Outlook/Apple) and the inline Outlook From:/Date:/Subject: header that leaked the quoted chain (b019343, 404b645)."
  - "Route replies by sender: requester→needs-response; agent (in forward_to)→threaded + relayed to requester from support@ + attention cleared; other internal→note, no relay (8c25219)."
  - "No mail loops: skip ingesting our own outbound (forwards/replies copied back to the mailbox); dedupe by message id (8c25219, 8eccba4)."
actions:
  - text: Inbound pipeline shipped (T-0177, T-0180 done); runs as a systemd 5-min timer. Needs Mail.Read + Mail.ReadWrite/Send on the Graph app.
    ticket: T-0180
side_quests:
  - Closed inbox tickets still showed as untriaged + a Tickets badge double-count → filed as a back-fill bug (a3d619a).
problem: support@ mail should become tickets, replies should thread onto them, and devs should be able to reply to requesters — a full email↔ticket loop.
success_criterion: An email to support@ opens a ticket; replies thread on; a forwarded ticket reaches an agent who replies from their own inbox and it's relayed back to the requester — no duplicates, no mail loops.
phase: build
---

# TS-005: Inbound email → tickets (Zendesk-style two-way inbox)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
