---
id: T-0267
title: Link follow-up tickets to their source (relates on update_ticket + show linked/spike-output tickets)
type: feature
state: triaged
created: 2026-06-05T22:52:59Z
updated: 2026-06-08T18:55:27Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - pm_update_ticket (MCP) accepts relates / blocks / blocked_by as optional arrays of ticket ids; invalid ids are rejected; the changed list is returned (mirrors how `labels` works — wholesale replace).
  - "Web parity: a ticket's links can be set/edited in the UI — a simple add/remove picker in the 'Related work' section (which today only DISPLAYS links)."
  - A finished spike surfaces the follow-up tickets it produced prominently (not buried in the Developer-details dropdown), so it's no longer a dead end.
  - The schema already defines relates/blocks/blocked_by/duplicates and delete already cleans them up — verify both still hold after the change.
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/update-ticket.ts
    symbol: updateTicketInput (add relates/blocks/blocked_by)
  - path: web/app/_actions/tickets.ts
    symbol: setTicketField (allow link arrays)
  - path: web/app/tickets/[id]/page.tsx
    symbol: Related work section (lines 365-374)
  - path: web/app/_components
    symbol: new LinksEditor.tsx
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