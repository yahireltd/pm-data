---
id: T-0267
title: Link follow-up tickets to their source (relates on update_ticket + show linked/spike-output tickets)
type: feature
state: done
created: 2026-06-05T22:52:59Z
updated: 2026-06-22T18:12:42Z
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
  - "[x] pm_update_ticket (MCP) accepts relates / blocks / blocked_by as optional arrays of ticket ids; invalid ids are rejected; the changed list is returned (mirrors how `labels` works — wholesale replace)."
  - "[x] Web parity: a ticket's links can be set/edited in the UI — a simple add/remove picker in the 'Related work' section (which today only DISPLAYS links)."
  - "[x] A finished spike surfaces the follow-up tickets it produced prominently (not buried in the Developer-details dropdown), so it's no longer a dead end."
  - "[x] The schema already defines relates/blocks/blocked_by/duplicates and delete already cleans them up — verify both still hold after the change."
  - "[x] DECIDE BEFORE CODING: 'wholesale replace like labels' (AC1) conflicts with the reciprocal-link invariant in cli/src/lib/links.ts (relates↔relates, blocks↔blocked_by). A blind replace leaves the inverse side stale and the link graph asymmetric — choose reciprocal-via-links.ts or a documented one-sided write first."
  - "[x] we also need a place to do this in the ui (create and link followup)"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/update-ticket.ts
    symbol: relates/blocks/blocked_by + reciprocal patchInverseSide
  - path: mcp-server/src/server.ts
    symbol: pm_update_ticket description
  - path: web/app/_actions/tickets.ts
    symbol: setTicketLinks action
  - path: web/app/_components/LinksEditor.tsx
    symbol: inline links editor (new)
  - path: web/app/tickets/[id]/page.tsx
    symbol: promoted Related work section + hasDeveloperDetails
  - path: web/app/docs/content.ts
    symbol: MCP-surface note
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260622-1745
    model: claude-opus-4-8
    started: 2026-06-22T17:45:09Z
    status: completed
    ended: 2026-06-22T17:59:30Z
    summary: |-
      **What we did:** Made it possible to link tickets together — "this relates to that," "this blocks that," "this is blocked by that" — from both the assistant's tools and the website. Adding a link automatically gives the other ticket the matching link back, so the connection always shows from both sides. We also moved the "Related work" panel out of the hidden developer-details drawer up into the main view, so a piece of exploratory work clearly points to the tickets it produced.

      **Why:** Until now the assistant couldn't set these links at all, and on the website they were read-only — you could see links but not add or remove them. So when a quick investigation produced follow-up tickets, there was no way to connect them back to it; the trail went cold.

      **If we did nothing:** Follow-up work would keep getting disconnected from where it came from — someone reading a finished investigation wouldn't see what it led to, and the assistant would keep being unable to wire related tickets together (a gap we hit repeatedly this very session).

      **The benefit:** Tickets now form a connected web you can navigate from either end, the links stay consistent on both sides automatically, and an investigation's outputs are visible right where you'd look for them.
    test_plan: |-
      Verified before handoff: MCP smoke test passed 8/8 (relates is reciprocal; blocks↔blocked_by inverse; wholesale unlink clears both sides; invalid-id and self-link rejected); full aggregate typecheck clean across all five packages; the web build passes (so the new client component + server action boundary is sound).

      Reviewer checklist:
      1. MCP (after deploy + MCP restart): call pm_update_ticket with relates:["T-XXXX"] on a ticket → both tickets show the relates link. Try blocks:["T-YYYY"] → the target gains blocked_by. Set relates:[] → both sides clear. Pass a non-existent id or the ticket's own id → rejected with a clear error.
         • Good real test: link T-0425 ↔ T-0421 (the id-overflow follow-up ↔ its source) — the link we couldn't make earlier today.
      2. Web (after deploy): open a ticket as a team member → a "Related work" section sits in the main column with three rows (Relates to / Blocks / Blocked by). Type a T-NNNN + Enter in a row → it's added; open that ticket → it shows the reciprocal link. Click the × on a chip → removed from both sides. Invalid id → inline error.
      3. Spike prominence: on a spike with follow-up links, confirm they show in the main column, not only in the collapsed Developer-details.
      4. Stakeholder view: confirm the Related-work editor is NOT shown to stakeholders.

      Notes: the reciprocal logic reuses the shared cli/src/lib/links.ts (the same path the CLI `pm link` uses) on the web; the MCP mirrors it (sets its own side wholesale + patches each target's inverse), so MCP/CLI/web stay consistent. The MCP server needs a restart to expose the new fields (a deploy restarts pm-mcp). The web 'Related work' editor I could not verify visually — please eyeball the layout at review.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Dev wiki (/docs MCP-surface) updated to note pm_update_ticket can set relates/blocks/blocked_by. The pm_update_ticket tool description was also updated.
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 16
branch: t0267-ticket-linking
---

## Problem

T-0249 (a security-review spike) confused the human because its 5 output tickets weren’t linked from it, and pm_update_ticket has no `relates` field to wire them. Agents should be able to link follow-ups to a parent and the UI should surface them.

## Context

Surfaced dogfooding on 2026-06-05 (T-0249 → T-0253–T-0257).

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — 3 of 4 criteria unbuilt (the 4th is already true).**

Confirms what we hit live this session: `pm_update_ticket` still can't set relates/blocks, the web "Related work" is display-only, and a spike's follow-ups stay buried in the Developer-details dropdown. The schema-defines-the-fields + delete-cleans-them-up criterion already holds. **One design call to make BEFORE coding** (now a 5th acceptance criterion): AC1 says "wholesale replace like labels," but `cli/src/lib/links.ts` keeps links reciprocal (relates↔relates, blocks↔blocked_by) — a blind replace leaves the other side stale and the link graph asymmetric. Decide reciprocal-via-links.ts vs a documented one-sided write first. Self-contained otherwise; schedule **last** so that call isn't rushed.

**2026-06-22 17:59 claude:** Run run-20260622-1745 completed — **What we did:** Made it possible to link tickets together — "this relates to that," "this blocks that," "this is blocked by that" — from both the assistant's tools and the website. Adding a link automatically gives the other ticket the matching link back, so the connection always shows from both sides. We also moved the "Related work" panel out of the hidden developer-details drawer up into the main view, so a piece of exploratory work clearly points to the tickets it produced.

**Why:** Until now the assistant couldn't set these links at all, and on the website they were read-only — you could see links but not add or remove them. So when a quick investigation produced follow-up tickets, there was no way to connect them back to it; the trail went cold.

**If we did nothing:** Follow-up work would keep getting disconnected from where it came from — someone reading a finished investigation wouldn't see what it led to, and the assistant would keep being unable to wire related tickets together (a gap we hit repeatedly this very session).

**The benefit:** Tickets now form a connected web you can navigate from either end, the links stay consistent on both sides automatically, and an investigation's outputs are visible right where you'd look for them.

---

**2026-06-22 18:12 — you**

done
