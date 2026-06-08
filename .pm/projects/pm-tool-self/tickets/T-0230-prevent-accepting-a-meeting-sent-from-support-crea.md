---
id: T-0230
title: "Prevent accepting a meeting sent from support creating a ticket "
type: support
state: review
priority: p2
created: 2026-06-05T15:23:19Z
updated: 2026-06-08T15:09:12Z
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
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - prevent accepting a meeting creating a ticket
out_of_scope: []
code_anchors:
  - path: comms/src/inbound/graph-poll.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1509
    model: claude-opus-4-8
    started: 2026-06-08T15:09:01Z
    status: completed
    ended: 2026-06-08T15:09:12Z
    summary: "Reconciliation — no new code. This was already fixed and shipped in an earlier session (commit e209cef, live): the system that turns incoming support emails into tickets now ignores calendar messages — meeting accepted / declined / tentative / cancelled notices — so replying to a meeting invite no longer creates a stray ticket. It recognises these two ways: Microsoft Graph tags calendar mail with a message type (language-independent), with a backup match on the standard subject prefixes. The ticket was left open after the fix shipped, so this run closes the loop and records it. Routed to review for your confirm-and-close in the sweep."
    test_plan: "Already live (shipped in e209cef, comms/src/inbound/graph-poll.ts). To confirm: accept a calendar invite sent to support@ → no ticket is created (the skip is logged); a normal support email still becomes a ticket as before. (No new code in this run — close-the-loop reconciliation of previously-shipped work.)"
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - inbound-email
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T15:09:12Z
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

**2026-06-05 21:26 claude:** **What we did** — The system that turns incoming support emails into tickets now ignores calendar messages — meeting accepted / declined / tentative / cancelled notices — so responding to a meeting invite no longer creates a stray ticket.

**Why** — Accepting a meeting was generating a bogus ticket from the calendar notification email.

**What would have happened if we did nothing** — The inbox would keep filling with junk tickets every time someone replied to a meeting invite, burying the real requests.

**The benefit** — A cleaner inbox: only genuine support emails become tickets.

(Fix is shipped + live; leaving this for you to confirm + close since it's your report.)

**2026-06-08 15:09 claude:** Run run-20260608-1509 completed — Reconciliation — no new code. This was already fixed and shipped in an earlier session (commit e209cef, live): the system that turns incoming support emails into tickets now ignores calendar messages — meeting accepted / declined / tentative / cancelled notices — so replying to a meeting invite no longer creates a stray ticket. It recognises these two ways: Microsoft Graph tags calendar mail with a message type (language-independent), with a backup match on the standard subject prefixes. The ticket was left open after the fix shipped, so this run closes the loop and records it. Routed to review for your confirm-and-close in the sweep.
