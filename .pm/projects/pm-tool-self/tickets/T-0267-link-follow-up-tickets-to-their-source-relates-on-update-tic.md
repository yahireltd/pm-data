---
id: T-0267
title: Link follow-up tickets to their source (relates on update_ticket + show linked/spike-output tickets)
type: feature
state: triaged
created: 2026-06-05T22:52:59Z
updated: 2026-06-05T22:52:59Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - pm_update_ticket (and the web) can set cross-links (relates/blocks)
  - A ticket shows its linked tickets; a spike shows the follow-up tickets it produced
  - A finished spike is no longer a dead end
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
---

## Problem

T-0249 (a security-review spike) confused the human because its 5 output tickets weren’t linked from it, and pm_update_ticket has no `relates` field to wire them. Agents should be able to link follow-ups to a parent and the UI should surface them.

## Context

Surfaced dogfooding on 2026-06-05 (T-0249 → T-0253–T-0257).