---
id: T-0177
title: Recieve and respond to ticket requests via email
type: feature
state: triaged
priority: p2
created: 2026-06-03T17:54:50Z
updated: 2026-06-03T18:14:30Z
project: pm-tool-self
section: null
parent: null
children: []
order: 48128
reporter: null
assignee: null
acceptance_criteria:
  - an email sent to support@yahire.com from anyuser@yahire.com should become a ticket
  - devs / can respond via email / ui
  - all responses are logged in the ticket
  - a way to remove chained emails so a reply doesnt include the original request etc - also remove signatures if possible
  - we use microsoft graph which as full access to all users inbox / sent etc so we should have a way to link responses to tickets
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
---

We did have this somewhere in the scope but not sure if it has been done at all / partially. It was meant to allow us to recieve tickets via support\@yahire.com (from @yahire.com emails only) and allow us to respond via email (also saves to comments) and also via the UI - the requesting user should recieve a response via either the UI / email. The person responding should be the from user to the requesting user
