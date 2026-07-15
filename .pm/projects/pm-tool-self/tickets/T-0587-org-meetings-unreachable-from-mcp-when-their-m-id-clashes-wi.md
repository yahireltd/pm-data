---
id: T-0587
title: Org meetings unreachable from MCP when their M-id clashes with a project meeting — add 'org' scope sentinel
type: bug
state: done
created: 2026-07-15T12:52:48Z
updated: 2026-07-15T15:26:04Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - '[x] pm_record_outcome / pm_get_meeting / pm_update_meeting_state / pm_delete_meeting accept project="org" and resolve the org-bucket meeting only'
  - '[x] The id-clash error message tells the caller to use "org" for the org-wide meeting'
  - "[x] Project-slug and no-scope behaviour unchanged (clash still throws rather than guessing)"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/lib/paths.ts
    symbol: findMeetingPath org sentinel
  - path: mcp-server/src/tools/record-outcome.ts
    symbol: project param description
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1253
    started: 2026-07-15T12:53:24Z
    status: completed
    ended: 2026-07-15T12:54:15Z
    summary: "Agents can now reach global (org-level) meetings even when their id number is shared with a project meeting. Trying to record the Ben→Rob call outcomes on the global weekly meeting M-010 failed: another project also has an M-010, the safety check that refuses to guess between them kicked in (correctly), but there was no way to say \"I mean the global one\" — so ANY global meeting with a duplicated number was simply unreachable for agents, on every meeting operation (read, record outcome, change state, delete). The fix: passing \"org\" as the project now targets the global bucket precisely, the error message tells you to do exactly that, and the tools' documentation says so. The website was never affected. If we'd left it, agents would stay locked out of exactly the meetings that matter most — the recurring global ones like the weekly M-010. Benefit: the weekly-meeting workflow (record outcomes, mark held, schedule next) fully works for agents."
    test_plan: '1. Deploy (restarts pm-mcp). 2. From an agent session: pm_get_meeting id=M-010 project="org" → returns the GLOBAL weekly meeting (not the sales-segmentation one). 3. pm_record_outcome meeting_id=M-010 project="org" → outcome lands on the global meeting (the Ben→Rob call outcomes will be added right after deploy as the live proof). 4. pm_get_meeting id=M-010 with NO project still errors with the ambiguity message, which now mentions using "org". 5. pm_get_meeting id=M-010 project="sales-segmentation-account-management" still returns the project one. 6. Regression: meeting tools on non-clashing ids behave as before.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - mcp
  - dogfood-finding
attention: null
version: 9
---

## Problem

Trying to record the Ben→Rob call outcomes on the GLOBAL weekly meeting M-010 failed: M-010 also exists in sales-segmentation, the ambiguity guard (T-0476) correctly refuses to guess — but the `project` disambiguator only accepts project slugs, so there was NO way to say "the org-wide one". Org meetings share the M- id space by design (ADR-016), so any org meeting with a clashing id is simply unreachable from every meeting MCP tool (get/record-outcome/update-state/delete). The web is unaffected (it resolves org meetings explicitly).

## Fix

`findMeetingPath` accepts the sentinel `project: "org"` → scans only the org bucket (`.pm/meetings/`). The ambiguity error now says so, and the four meeting tools' `project` descriptions document it.

## Conversation

**2026-07-15 12:54 claude-code:** Run run-20260715-1253 completed — Agents can now reach global (org-level) meetings even when their id number is shared with a project meeting. Trying to record the Ben→Rob call outcomes on the global weekly meeting M-010 failed: another project also has an M-010, the safety check that refuses to guess between them kicked in (correctly), but there was no way to say "I mean the global one" — so ANY global meeting with a duplicated number was simply unreachable for agents, on every meeting operation (read, record outcome, change state, delete). The fix: passing "org" as the project now targets the global bucket precisely, the error message tells you to do exactly that, and the tools' documentation says so. The website was never affected. If we'd left it, agents would stay locked out of exactly the meetings that matter most — the recurring global ones like the weekly M-010. Benefit: the weekly-meeting workflow (record outcomes, mark held, schedule next) fully works for agents.

---

**2026-07-15 15:26 — you**

done tool could find the meeting after fix
