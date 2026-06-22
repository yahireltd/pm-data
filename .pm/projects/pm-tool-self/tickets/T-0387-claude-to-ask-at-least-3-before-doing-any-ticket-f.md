---
id: T-0387
title: Claude to ask at least 3 before doing any ticket for context
type: feature
state: triaged
priority: p2
created: 2026-06-16T16:50:03Z
updated: 2026-06-22T16:13:37Z
project: pm-tool-self
section: null
parent: null
children: []
order: 101376
reporter: null
assignee: null
acceptance_criteria:
  - When an agent claims a ticket, it asks at least 3 clarifying questions (or as many as needed) before starting work, unless the scope is crystal-clear
  - The rule is written into AGENTS.md so agents apply it consistently
  - Clearly-scoped tickets may skip the questions, but asking is the default when scoping is not crystal-clear
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
version: 3
collaborators:
  - kind: human
    name: Austin Pickering
---

When a ticket is claimed for the agent. ask at least three questions - or as many as needed to get all the context needed. This needs to also be a rule for agents to start a ticket. There may be some where the requirements are scoped correctly and do not need further questions but in most cases ask questions unless crystal clear scoping
