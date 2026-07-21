---
id: T-0617
title: MCP tools to update a recorded outcome + replace a meeting transcript (re-source outcomes from a better transcript)
type: feature
state: done
created: 2026-07-17T19:43:54Z
updated: 2026-07-21T17:28:35Z
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
  - "[x] pm_update_outcome edits an outcome's owner/description/due by index without touching the others; clear_owner removes the owner"
  - "[x] pm_attach_meeting_transcript with replace:true swaps an existing transcript (attachment + body section) for a new one; without replace, duplicate still rejected"
  - "[x] Replace excises only the matching transcript's body section, not a second audio's transcript on the same meeting"
  - "[x] Used live to re-attribute M-003's outcomes and put the speaker-labelled transcript on the meeting"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/record-outcome.ts
    symbol: runUpdateOutcome
  - path: mcp-server/src/tools/meeting-audio.ts
    symbol: runAttachMeetingTranscript replace
  - path: mcp-server/src/server.ts
    symbol: pm_update_outcome registration
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1945
    started: 2026-07-17T19:45:10Z
    status: completed
    ended: 2026-07-17T19:45:31Z
    summary: "We can now correct a recorded meeting outcome and swap in a better transcript — the pieces needed to re-source the accounts outcomes from the speaker-labelled version. Two capabilities were added. First, an outcome can be edited in place: its wording, its owner (who decided), or its due date — without disturbing the others or losing its history — which is what lets us credit a decision to the right person once we know who actually said it (e.g. the \"VAT adjustment\" label was Effie's idea, not the meeting lead's). Second, a meeting's transcript can now be replaced rather than duplicated, so the plain transcript can be overwritten with the speaker-labelled one, cleanly and without harming a second recording's transcript on the same meeting. Both are agent tools; the website is unaffected. Once deployed, the next step is to put M-003's speaker transcript on the meeting and refine the handful of outcomes where the speaker version changes who-decided. Benefit: outcomes can be made accurate about attribution after the fact, and transcripts upgraded in place."
    test_plan: "SETUP: deploy (restarts pm-mcp with the new tools). 1. pm_update_outcome on M-003 (project=accounts-integrity) index N with a new owner → that outcome's owner changes, others untouched, recorded_at preserved; clear_owner=true removes an owner. 2. pm_attach_meeting_transcript for M-003's audio with replace:true → the plain transcript (attachment + \"## Transcript\" body section) is swapped for the speaker-labelled one; without replace, it still errors \"already has a transcript\". 3. Replace safety: on a meeting with two audio files' transcripts, replacing one leaves the other intact. 4. LIVE APPLICATION (the real proof): after deploy, re-attribute M-003's outcomes (credit Effie for the VAT-adjustment label, Jhuztine for surfacing the archive issue) and attach the speaker transcript — via a fresh MCP client. 5. tools-list test updated (pm_update_outcome)."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - mcp
  - meetings
attention: null
version: 10
---

## Problem

M-003's 13 outcomes were drafted from the PLAIN transcript. The speaker-labelled transcript now reveals who actually said what (e.g. the "VAT adjustment" label was Effie's idea, credited to Austin in the plain-sourced outcome). But there was no way to (a) fix an outcome's attribution in place, or (b) swap the plain transcript on the meeting for the speaker version — pm_attach_meeting_transcript rejected the duplicate.

## Fix

- pm_update_outcome(meeting_id, project?, index, description?, owner?, clear_owner?, due?) — edit an existing outcome in place; recorded_at + follow_up_ticket preserved.
- pm_attach_meeting_transcript gains replace:true — drop the old transcript attachment + its "## Transcript" body section (surgically, not collateral to a second audio's section) and write the new one.

## Conversation

**2026-07-17 19:45 claude-code:** Run run-20260717-1945 completed — We can now correct a recorded meeting outcome and swap in a better transcript — the pieces needed to re-source the accounts outcomes from the speaker-labelled version. Two capabilities were added. First, an outcome can be edited in place: its wording, its owner (who decided), or its due date — without disturbing the others or losing its history — which is what lets us credit a decision to the right person once we know who actually said it (e.g. the "VAT adjustment" label was Effie's idea, not the meeting lead's). Second, a meeting's transcript can now be replaced rather than duplicated, so the plain transcript can be overwritten with the speaker-labelled one, cleanly and without harming a second recording's transcript on the same meeting. Both are agent tools; the website is unaffected. Once deployed, the next step is to put M-003's speaker transcript on the meeting and refine the handful of outcomes where the speaker version changes who-decided. Benefit: outcomes can be made accurate about attribution after the fact, and transcripts upgraded in place.

---

**2026-07-21 17:28 — you**

DONE
