---
id: T-0177
title: Recieve and respond to ticket requests via email
type: feature
state: done
priority: p2
created: 2026-06-03T17:54:50Z
updated: 2026-06-03T19:46:07Z
project: pm-tool-self
section: null
parent: null
children: []
order: 48128
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - an email sent to support@yahire.com from anyuser@yahire.com should become a ticket
  - devs / can respond via email / ui
  - all responses are logged in the ticket
  - a way to remove chained emails so a reply doesnt include the original request etc - also remove signatures if possible
  - we use microsoft graph which as full access to all users inbox / sent etc so we should have a way to link responses to tickets
  - the person sending the ticket via email is automatically a stakeholder and any update should go to the stakeholder(s)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-1821
    model: claude-code
    started: 2026-06-03T18:21:01Z
    status: needs_review
    summary: 'Built the full inbound-email round-trip (commit b019343, sprint SPR-021): support@ mail → tickets, replies thread onto the originating ticket by Graph conversationId, quoted-chain/signature cleaning, @yahire.com sender allowlist, and a "Reply to requester" composer that threads via Graph createReply from the support mailbox. Most of the pipeline already existed but was dormant; this wires it up, fixes the schema so polled tickets pass lint, and adds threading + reply-out. Needs push + sudo pm-deploy, plus three ops prerequisites before it goes live (below).'
    ended: 2026-06-03T18:46:27Z
    test_plan: |-
      Ship: `git push origin master` + `sudo pm-deploy`.

      **Ops prerequisites (Austin):**
      1. Grant the Graph app (client fa04e95a-…) **Mail.Read** (poll) + **Mail.ReadWrite & Mail.Send** (reply createReply/send) on **support@yahire.com**, and confirm that mailbox/alias exists.
      2. Enable inbound in the live `pm-data` `.pm/config.yml` (see comms/README): mailbox `support@yahire.com`, `allow_domains: [yahire.com]`, `enabled: true`.
      3. Install the `pm-comms-poll-inbox` systemd oneshot+timer (deploy-ubuntu.md), or run `bun comms/bin/pm-comms poll-inbox` once to smoke-test.

      **Verify:**
      1. Email support@ from a @yahire.com address → a clean ticket appears in /inbox (quoted chains/signatures stripped; reporter = sender; conversation_id set). A non-@yahire.com sender is skipped.
      2. Reply to that thread → it appends to the SAME ticket's ## Conversation (no duplicate ticket), and the ticket flags attention "needs response".
      3. On the ticket page, "Reply to requester" → requester receives a threaded email and the reply is logged to ## Conversation; plain Comments stay internal.
      4. `pm-lint` passes on the generated tickets.
labels: []
attention: null
backlog_status: confirmed_for_release
---

We did have this somewhere in the scope but not sure if it has been done at all / partially. It was meant to allow us to recieve tickets via support\@yahire.com (from @yahire.com emails only) and allow us to respond via email (also saves to comments) and also via the UI - the requesting user should recieve a response via either the UI / email. The person responding should be the from user to the requesting user

## Conversation

**2026-06-03 19:46 — you**

all working as expected now
