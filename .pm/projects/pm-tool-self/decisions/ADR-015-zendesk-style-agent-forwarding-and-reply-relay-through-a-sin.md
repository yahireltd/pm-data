---
id: ADR-015
slug: zendesk-style-agent-forwarding-and-reply-relay-through-a-sin
title: Zendesk-style agent forwarding and reply relay through a single shared support@ mailbox
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0180
  - T-0177
---

## Context
Once inbound emails became tickets, agents needed to act on them and customers needed replies — without giving every agent access to the support inbox or exposing internal addresses to requesters. We could have built an in-app-only reply composer (forcing agents into the tool), or given each agent their own customer-facing identity (leaking internal addresses, fragmenting the thread). We also had to decide which mailbox is the system's outward face.

## Decision
Route everything through one shared mailbox (moved from it@ to support@yahire.com as the deliberate outward identity). On a new ticket the poller forwards it to the configured agent list with Reply-To set back to the support mailbox; agents reply from their own inbox, that reply returns to support@, the poller threads it onto the ticket, and — because the sender is in forward_to — relays it on to the requester via an in-thread Graph createReply so the customer always sees 'support', never the agent. A requester reply instead sets attention=human; any other internal sender is just a note. Agents work entirely from their normal email client.

## Consequences
Agents never need pm-tool or inbox access, the customer-facing identity stays consistently support@, and the full exchange is captured on the ticket. The price: support@ is a single shared identity (no per-agent attribution to the customer) and a routing single point of failure, correct relay depends on sender-role classification (requester vs agent vs other) and the Reply-To/forward_to wiring, and the support mailbox sees its own outbound, which must be explicitly excluded from re-ingestion.