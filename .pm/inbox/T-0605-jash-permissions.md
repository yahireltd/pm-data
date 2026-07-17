---
id: T-0605
title: Jash permissions
type: support
state: inbox
priority: p2
created: 2026-07-16T17:16:44Z
updated: 2026-07-17T04:56:26Z
project: null
section: null
parent: null
children: []
order: 1024
reporter:
  kind: customer
  name: Austin Pickering
  channel: email
  contact: austin@yahire.com
assignee: null
acceptance_criteria: []
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
customer_impact: Been having difficulty a few times giving users access. When they have something inherited as a child permission, it doesn’t seem to work as intended. Trying to give Jash /managemet/targets went to that page - it shows managers have it as a child permission. He was already in man
intake_channel: email
intake_mailbox: support@yahire.com
intake_message_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdklLOjAAA=
intake_message_ids:
  - AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdklLOjAAA=
  - AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdlml1lAAA=
conversation_id: AAQkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgAQAJ4agawobSdJvWvfLMwVtlk=
version: 2
---

## Request

**From:** Austin Pickering <austin@yahire.com>  
**Subject:** Jash permissions  
**Received:** 2026-07-16T17:16:44Z

## Conversation

**2026-07-16 17:16 — Austin Pickering <austin@yahire.com>**

Been having difficulty a few times giving users access. When they have something inherited as a child permission, it doesn’t seem to work as intended.

Trying to give Jash /managemet/targets went to that page - it shows managers have it as a child permission.

He was already in managers group. Then tried to give manager permissions that didn’t work.

Then I tried to assign direct to his users and that didn’t work either. I have bumped into this every time I see a page where they have that inherited via child row on a particular route.

---

**2026-07-17 04:56 — Zsolt <zsolt@yahire.com>**

Hi Austin,

So you run into 2 different issues, but everything working as intended.

First, the targets page have an on page check for users

I added Jashes ID to it.

And second, the Management permission doesn’t have management/*, and ops-hours, ops-hours-search are missing from it, so that’s why it didn’t worked when you added management permission to his user.

So it was not RBAC issue, it is permission assignment issue and on page user check issue.

It should work now, I checked with his user.

Thanks

From: Austin Pickering <austin@yahire.com>
Date: Thursday, 16 July 2026 at 18:16
To: Zsolt <zsolt@yahire.com>; Yahire Support <support@yahire.com>
Subject: Jash permissions

Been having difficulty a few times giving users access. When they have something inherited as a child permission, it doesn’t seem to work as intended.

Trying to give Jash /managemet/targets went to that page - it shows managers have it as a child permission.

He was already in managers group. Then tried to give manager permissions that didn’t work.

Then I tried to assign direct to his users and that didn’t work either. I have bumped into this every time I see a page where they have that inherited via child row on a particular route.
