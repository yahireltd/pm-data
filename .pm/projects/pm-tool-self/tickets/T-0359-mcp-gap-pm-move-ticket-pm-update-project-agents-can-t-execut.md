---
id: T-0359
title: "MCP gap: pm_move_ticket + pm_update_project — agents can't execute data tidy-ups (T-0353 blocker)"
type: feature
state: review
created: 2026-06-11T23:40:34Z
updated: 2026-06-12T01:17:42Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - pm_move_ticket moves a ticket inbox↔project / project→project preserving id, body and conversation; recomputes order in the target; inbox→triaged state shift on entering a project; validates the target project/section exists
  - Cross-project moves CLEAR the surface tag (project-scoped, T-0351) and KEEP the pinned branch (repo/stream-scoped, T-0352) — mirroring the web moveTicket exactly
  - pm_update_project edits name / state / kind / goal; state obeys the shared project state machine (illegal transitions rejected); slug and id are immutable
  - Both tools registered with descriptions; dev wiki MCP table + README write list updated in the same change
  - "Proof: T-0353's migration is executable end-to-end via MCP (moves + surface tags + branch pins + renames + retirements)"
out_of_scope:
  - Project hard-delete (retire = state done/cancelled with a pointer comment)
  - Slug renames / file relocations for projects
  - Bulk-move endpoints (loop the single tool)
code_anchors:
  - path: mcp-server/src/tools/move-ticket.ts
    symbol: NEW — mirror web/app/_actions/tickets.ts moveTicket
  - path: mcp-server/src/tools/update-project.ts
    symbol: NEW — name/state/kind/goal; import isLegalProjectTransition from cli/src/lib/state-machine.ts
  - path: mcp-server/src/server.ts
    symbol: register both after pm_update_ticket
  - path: web/app/_actions/tickets.ts
    symbol: moveTicket — the behaviour to mirror (surface cleared, branch kept, order recompute)
  - path: web/app/docs/content.ts
    symbol: MCP write table
  - path: README.md
    symbol: MCP write list
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2340
    model: claude-fable-5
    started: 2026-06-11T23:40:54Z
    status: completed
    progress:
      - at: 2026-06-11T23:43:46Z
        note: "Both tools implemented: agents can now move tickets between projects (keeping their id, history and pinned branch; surface tags clear as designed) and edit a project's name, lifecycle state and kind — with the same legality rules the web enforces (a finished project can't be resurrected). Eleven-case test suite passes including the file relocation, state shifts and rejection paths. Adversarial review running before commit; one more deploy after that and the consolidation runs."
    ended: 2026-06-12T01:17:42Z
    summary: |-
      **What we did**
      Gave the AI two missing abilities: moving a ticket between projects (keeping its number, history and conversation) and editing a project's name, lifecycle state and kind. Both follow exactly the same rules as the buttons in the web app — an unknown destination is refused, a finished project can't be brought back, and moving work clears its site tag but keeps its pinned branch.

      **Why we did it**
      The project tidy-up (merging duplicated projects) was ready to run, but the AI physically couldn't do it — those operations only existed as buttons in the web app. The working rule is: if a tool is missing to do the job properly, add the tool.

      **If we did nothing**
      Every future reorganisation — and there will be more as projects evolve — would be a manual click-through for a person, with the AI able to plan it but not execute it.

      **The benefit**
      Proven immediately: the consolidation ran the same evening using these tools — eight tickets relocated, three duplicated projects retired with pointer notes, and the ERP project renamed — with every move verified afterwards.
    test_plan: |-
      Largely proven in production already (the T-0353 consolidation ran through these tools). To double-check:
      1. Open any moved ticket (e.g. T-0347 or T-0218) → it opens at /tickets/<id> as before, with its full conversation history, original number, and its review state and attention flag intact.
      2. The retired projects (Yasite, Yahire Website Backend, Logistics Route Planning rollout) sit in the dimmed Closed sidebar group after a refresh, each with a pointer note in its goal explaining where its work went.
      3. MCP edge cases: pm_move_ticket with a made-up project → "project not found"; with a made-up section → refused listing the real sections; pm_update_project trying to reactivate a done project → refused.
      4. Cross-impact: the ordinary web Move button and project editors behave exactly as before (the MCP tools are additive; no shared code paths were changed).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Dev wiki MCP table + README write list — same commits.
labels:
  - dogfood
  - mcp
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-12T01:17:42Z
version: 5
---

## Problem

T-0353 (consolidate duplicated projects) is unblocked — T-0350/T-0351/T-0352 are deployed — but an agent cannot execute it: the MCP has no way to move a ticket between projects (web UI + server CLI only) and no way to edit a project's name/state/kind. AGENTS.md §10: missing a tool to satisfy the work the right way → add the tool.

## Design

- **pm_move_ticket** `{ticket_id, project?|to_inbox, section?}` — mirrors the web moveTicket semantics exactly: write to the new path, remove the old file, recompute order among target peers, inbox→triaged on entering a project, validate target, clear `surface` on cross-project moves (T-0351), keep `branch` (T-0352), touch both projects.
- **pm_update_project** `{slug, name?, state?, kind?, goal?}` — partial patch; state transitions validated by the SHARED state machine (import from cli/src/lib, the complete-run precedent); kind validated against initiative|system. slug/id immutable.

Ratified by Austin 2026-06-12 (execution path for T-0353: build tools → deploy → agent runs the migration).

## Conversation

**2026-06-12 01:17 claude-code:** Run run-20260611-2340 completed — **What we did**
Gave the AI two missing abilities: moving a ticket between projects (keeping its number, history and conversation) and editing a project's name, lifecycle state and kind. Both follow exactly the same rules as the buttons in the web app — an unknown destination is refused, a finished project can't be brought back, and moving work clears its site tag but keeps its pinned branch.

**Why we did it**
The project tidy-up (merging duplicated projects) was ready to run, but the AI physically couldn't do it — those operations only existed as buttons in the web app. The working rule is: if a tool is missing to do the job properly, add the tool.

**If we did nothing**
Every future reorganisation — and there will be more as projects evolve — would be a manual click-through for a person, with the AI able to plan it but not execute it.

**The benefit**
Proven immediately: the consolidation ran the same evening using these tools — eight tickets relocated, three duplicated projects retired with pointer notes, and the ERP project renamed — with every move verified afterwards.
