---
id: T-0180
title: Forward inbound tickets to agents + relay agent email replies (Zendesk-style shared inbox)
type: feature
state: triaged
created: 2026-06-03T19:18:22Z
updated: 2026-06-03T19:21:55Z
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
  - A new inbound ticket is forwarded from support@ to every address in inbound_email.forward_to, with Reply-To set to support@
  - An agent reply (sender in forward_to) that returns to support@ is threaded onto the ticket AND relayed to the requester from support@, and the ticket's attention is cleared
  - A requester reply is threaded and flags needs-response (unchanged); an internal sender not in forward_to is threaded without relaying
  - forward_to is configurable in inbound_email and documented in comms/README; no mail loops and agent replies are deduped so they are never relayed twice
out_of_scope: []
code_anchors:
  - path: comms/src/inbound/graph-poll.ts
    note: forward on create + sender-type routing + relay
  - path: comms/src/channels/email.ts
    note: forwardEmail (Reply-To) + reuse sendReply for relay
  - path: comms/src/types.ts
    note: inbound_email.forward_to
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - comms
  - inbound-email
attention: null
---

## Problem

Inbound support tickets (T-0177) land in pm-tool, but agents (zsolt@, austin@) don't see them in their own inbox and can't reply from there the way they would in Zendesk.

## Approach (decided with Austin)

- **Forward**: on a new inbound ticket, the support mailbox forwards the request to a configured agent list (`inbound_email.forward_to`), sent **from `support@` with `Reply-To: support@`** — so an agent's plain Reply returns to the support mailbox.
- **Capture + relay**: the existing poller reads `support@`, so an agent's reply lands there. The poller tells the sender apart:
  - **requester** (== `reporter.contact`) → thread + flag "needs response" (current behavior).
  - **agent** (in `forward_to`) → thread + **relay to the requester** (from `support@`, threaded) + clear attention.
  - other internal sender → thread as an internal note, no relay.
- UI already separates customer-facing ("Reply to requester") from internal (Comments), so the relay-on-email rule only applies to inbound agent emails.
- No mail loops; agent replies are deduped by message id so they're never re-relayed.

## Out of scope

- Polling agents' individual mailboxes (we use Reply-All→support@ instead).

## Conversation

**2026-06-03 19:21 claude-code:** Implemented & committed (`8c25219`), in SPR-021 — awaiting push + `sudo pm-deploy`.

- New inbound tickets forward from `support@` to `inbound_email.forward_to` (zsolt@, austin@) with **Reply-To: support@**.
- Poller routes replies by sender: requester → needs-response; agent (in `forward_to`) → threaded + **relayed to the requester** + attention cleared; other internal sender → note only. Deduped by message id (no re-relay), no mail loops.

**Blocker for the send side:** forward + relay (and the UI "Reply to requester") send from `support@`, so they need **Mail.Send** granted on the Graph app — currently only Mail.ReadWrite is granted, so intake/threading work but outbound sends will stub until Mail.Send + admin consent is added.
