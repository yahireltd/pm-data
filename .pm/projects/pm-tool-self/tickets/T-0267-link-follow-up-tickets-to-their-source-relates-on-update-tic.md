---
id: T-0267
title: Link follow-up tickets to their source (relates on update_ticket + show linked/spike-output tickets)
type: feature
state: in_progress
created: 2026-06-05T22:52:59Z
updated: 2026-06-22T17:45:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - pm_update_ticket (MCP) accepts relates / blocks / blocked_by as optional arrays of ticket ids; invalid ids are rejected; the changed list is returned (mirrors how `labels` works — wholesale replace).
  - "Web parity: a ticket's links can be set/edited in the UI — a simple add/remove picker in the 'Related work' section (which today only DISPLAYS links)."
  - A finished spike surfaces the follow-up tickets it produced prominently (not buried in the Developer-details dropdown), so it's no longer a dead end.
  - The schema already defines relates/blocks/blocked_by/duplicates and delete already cleans them up — verify both still hold after the change.
  - "DECIDE BEFORE CODING: 'wholesale replace like labels' (AC1) conflicts with the reciprocal-link invariant in cli/src/lib/links.ts (relates↔relates, blocks↔blocked_by). A blind replace leaves the inverse side stale and the link graph asymmetric — choose reciprocal-via-links.ts or a documented one-sided write first."
  - we also need a place to do this in the ui (create and link followup)
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
agent_runs:
  - id: run-20260622-1745
    model: claude-opus-4-8
    started: 2026-06-22T17:45:09Z
    status: in_progress
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 6
---

## Problem

T-0249 (a security-review spike) confused the human because its 5 output tickets weren’t linked from it, and pm_update_ticket has no `relates` field to wire them. Agents should be able to link follow-ups to a parent and the UI should surface them.

## Context

Surfaced dogfooding on 2026-06-05 (T-0249 → T-0253–T-0257).

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — 3 of 4 criteria unbuilt (the 4th is already true).**

Confirms what we hit live this session: `pm_update_ticket` still can't set relates/blocks, the web "Related work" is display-only, and a spike's follow-ups stay buried in the Developer-details dropdown. The schema-defines-the-fields + delete-cleans-them-up criterion already holds. **One design call to make BEFORE coding** (now a 5th acceptance criterion): AC1 says "wholesale replace like labels," but `cli/src/lib/links.ts` keeps links reciprocal (relates↔relates, blocks↔blocked_by) — a blind replace leaves the other side stale and the link graph asymmetric. Decide reciprocal-via-links.ts vs a documented one-sided write first. Self-contained otherwise; schedule **last** so that call isn't rushed.
