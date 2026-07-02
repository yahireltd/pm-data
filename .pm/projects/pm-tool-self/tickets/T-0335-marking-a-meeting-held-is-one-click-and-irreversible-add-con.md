---
id: T-0335
title: Marking a meeting "held" is one click and irreversible — add confirm + a way back
type: bug
state: review
created: 2026-06-10T00:12:38Z
updated: 2026-07-02T12:39:31Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Flipping a meeting to held asks for confirmation (it is a one-way transition)
  - An admin can revert held back to scheduled when it was a mis-click (audited, like other admin overrides)
  - "Stretch: a pm_update_meeting_state MCP tool so agents can fix/operate meeting states too (known gap)"
out_of_scope: []
code_anchors:
  - path: web/app/_components/MeetingDetailHeader.tsx
    symbol: onStateSelect / ConfirmDialog / revert
  - path: web/app/_actions/meetings.ts
    symbol: revertMeetingHeld
  - path: mcp-server/src/tools/update-meeting-state.ts
    symbol: pm_update_meeting_state (stretch)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1239
    started: 2026-07-02T12:39:15Z
    status: completed
    ended: 2026-07-02T12:39:31Z
    summary: "Marking a meeting \"held\" now asks you to confirm first, and a mis-click can be undone. Before, changing a meeting to held was a single, silent dropdown change that couldn't be reversed — the one time the wrong meeting got marked held, someone had to edit the file over SSH to fix it, and it also fired the minutes email to attendees. Now: choosing \"held\" (or \"cancelled\") pops a confirmation that spells out it's a one-way step; and if a held meeting was a mistake, an admin can pick \"scheduled (revert)\" to move it back, which is recorded on the meeting and does not re-send any emails. Non-admins don't see a revert option they can't use. Agents got the same ability via a new tool so they can help fix meeting states too. If we'd left it, more wrong-meeting mis-clicks were inevitable as the sales team and Axel start using meetings heavily — each one needing a developer to unpick. Benefit: meeting state is safe to change and easy to correct."
    test_plan: "1. Open a scheduled meeting. Change the state dropdown to \"held\" → a confirm dialog appears explaining it's one-way and emails minutes. Cancel → state stays scheduled. Confirm → it becomes held (and, per T-0357, minutes go only to subscribers). 2. As an ADMIN, on the held meeting the dropdown shows \"scheduled (revert)\"; pick it → a revert dialog (with an optional reason) appears. Confirm → meeting returns to scheduled, an audit line is appended to the meeting body (\"Reverted held → scheduled by <you> …\"), and NO emails are sent. 3. As a NON-admin, a held meeting's dropdown does NOT offer scheduled; if forced, the server refuses with an admin-only error. 4. Changing to \"cancelled\" also asks to confirm; a cancelled meeting is terminal (no other options). 5. Backdrop-click / Esc cancels either dialog. 6. MCP (after deploy + session restart): pm_update_meeting_state moves scheduled→held, held→cancelled, and held→scheduled (revert, audited); it rejects illegal transitions and does not email minutes. Note: new MCP tools require an MCP session restart to be callable."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T12:39:31Z
version: 6
collaborators:
  - kind: human
    name: Austin Pickering
---

## Problem

The meeting state machine is deliberately one-way (scheduled → held → cancelled), but the web exposes "mark held" as a single un-confirmed click. Tonight Austin marked the WRONG meeting held (M-005, a future workshop) while intending M-006, and there was no way back in the UI — the legal-transitions table has no held → scheduled. It took a developer with SSH editing the file directly to undo one mis-click.

## Context

Real incident, 2026-06-10 ~00:10: two similar meetings under PP-002 (the Apr kickoff to be marked held + Thursday's upcoming workshop); the wrong one got the click. With more humans (sales team, Axel) about to use meetings heavily for the Sales Coherence programme, this will recur.

## Design notes

Confirm dialog on held (cheap); an admin-only "revert to scheduled" escape hatch with an audit note (the pattern the danger-zone actions already use). Related known gap: no MCP tool changes meeting state, so agents can't help fix these either.

## Conversation

**2026-07-02 12:39 claude-code:** Run run-20260702-1239 completed — Marking a meeting "held" now asks you to confirm first, and a mis-click can be undone. Before, changing a meeting to held was a single, silent dropdown change that couldn't be reversed — the one time the wrong meeting got marked held, someone had to edit the file over SSH to fix it, and it also fired the minutes email to attendees. Now: choosing "held" (or "cancelled") pops a confirmation that spells out it's a one-way step; and if a held meeting was a mistake, an admin can pick "scheduled (revert)" to move it back, which is recorded on the meeting and does not re-send any emails. Non-admins don't see a revert option they can't use. Agents got the same ability via a new tool so they can help fix meeting states too. If we'd left it, more wrong-meeting mis-clicks were inevitable as the sales team and Axel start using meetings heavily — each one needing a developer to unpick. Benefit: meeting state is safe to change and easy to correct.
