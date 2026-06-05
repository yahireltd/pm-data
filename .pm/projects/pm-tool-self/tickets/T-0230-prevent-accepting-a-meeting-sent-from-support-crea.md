---
id: T-0230
title: "Prevent accepting a meeting sent from support creating a ticket "
type: support
state: triaged
priority: p2
created: 2026-06-05T15:23:19Z
updated: 2026-06-05T18:13:53Z
project: pm-tool-self
section: null
parent: null
children: []
order: 68608
reporter:
  kind: customer
  name: Austin Pickering
  channel: email
  contact: austin@yahire.com
assignee: null
acceptance_criteria:
  - prevent accepting a meeting creating a ticket
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - inbound-email
attention: null
customer_impact: "Accepted: Logistics Rollout meeting"
intake_channel: email
intake_mailbox: support@yahire.com
intake_message_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdKEFn7AAA=
intake_message_ids:
  - AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdKEFn7AAA=
conversation_id: AAQkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgAQAJhsYViIFzZBtoJQ2qrHjTc=
---

Accepting a meeting creates a ticket incorrectly see below - i accepted a meeting and that then generated a ticket. We need to prevent this\
\
Request
-------

**From:** Austin Pickering <austin@yahire.com>\
**Subject:** Accepted: Logistics Rollout meeting\
**Received:** 2026-06-05T15:23:19Z

## Conversation

**2026-06-05 15:23 — Austin Pickering** **<austin@yahire.com>**

_(empty body)_

**2026-06-05 16:57 claude-code:** Fix shipped (live now). The inbound email handler now ignores calendar mail — meeting accept/decline/tentative/cancel notices — so responding to a meeting invite no longer opens a ticket. It detects these two ways: Microsoft Graph tags meeting mails with a calendar type (works regardless of language), and as a backup we match the standard subject prefixes ("Accepted:", "Declined:", "Tentative:", "Canceled:").

To confirm: accept a calendar invite sent to support@ — no ticket should be created (and the skip is logged). A normal support email still becomes a ticket as before.

This is your ticket (assigned to you, from your inbound report), so I've left it for you to close once you've confirmed. Code: comms/src/inbound/graph-poll.ts.
