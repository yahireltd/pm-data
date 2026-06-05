---
id: ADR-030
slug: tickets-can-be-permanently-deleted-admin-only-trading-the-au
title: Tickets can be permanently deleted (admin-only), trading the audit trail for a clean board
state: accepted
decided: 2026-06-05
decided_by:
  - austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0226
---

## Context

There was no way to remove a ticket from any screen. The existing "won't fix / duplicate" close keeps the ticket on the board as a card with its history — which is right for real work, but useless for scratch and test junk (e.g. tickets created while trying the tool out) that should simply disappear.

## Decision

We added a true delete that removes the ticket entirely. It is deliberately gated: in the web app only an admin sees it and they must confirm; over the agent tools it requires an explicit confirm flag; on the command line it requires a --force flag. Before removing a ticket we also clean up any links other tickets had to it, so nothing is left pointing at something that no longer exists.

We chose a real delete over only offering the softer "won't fix" close, because junk should leave no trace.

## Consequences

- Scratch and junk tickets can finally be cleared, keeping the board honest.
- A deleted ticket is gone for good — we knowingly give up the permanent file-based record for these items (the project's general principle is that the files are the source of truth), which is why deletion is admin-only and requires confirmation.
- Only tickets can be hard-deleted so far; the same need will likely come up for other throwaway items (e.g. test sprints), which is a future follow-up.