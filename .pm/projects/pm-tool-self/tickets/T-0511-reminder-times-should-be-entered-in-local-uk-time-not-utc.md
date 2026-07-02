---
id: T-0511
title: Reminder times should be entered in local (UK) time, not UTC
type: bug
state: done
created: 2026-07-02T17:45:45Z
updated: 2026-07-02T18:44:56Z
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
  - "[x] The reminder time field takes local (UK) wall-clock time — entering 18:45 fires at 18:45 UK and shows 18:45 in the list"
  - "[x] Editing a reminder shows the time in local time (stored UTC converted back), and re-saving without changes doesn't shift it"
  - "[x] A time near midnight that crosses the UTC date boundary stores the correct UTC date (fires at the right instant)"
  - "[x] The list row's anchor date/time displays in local time when a time is set; date-only reminders are unchanged"
out_of_scope: []
code_anchors:
  - path: web/app/reminders/RemindersManager.tsx
    symbol: localToUtc / utcToLocal at the form boundary
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1745
    started: 2026-07-02T17:45:55Z
    status: completed
    ended: 2026-07-02T17:47:58Z
    summary: "Reminder times now work in UK time, the way you'd expect. Before, the time box quietly wanted UTC: you typed 18:45 meaning UK time, but the list then showed 19:45 because Britain is an hour ahead of UTC in summer — so what you typed and what you saw never matched, and you had to do the mental maths yourself (entering 17:45 to get 18:45). Now you type the time as it reads on your clock, and that same time is what shows everywhere — the list, the edit form, and when it actually fires. Under the hood it's still stored in the universal format the scheduler needs (including handling the clocks-change days and times near midnight correctly), so nothing about the sending machinery changed. If we'd left it, every timed reminder would risk firing an hour off or forcing UTC arithmetic on whoever sets it. Benefit: what you type is what you get."
    test_plan: "1. After deploy, create a reminder with today's date and a time a few minutes ahead AS UK TIME (no mental UTC maths). Save → the list row's \"anchor\" and \"next:\" both show the time you typed. 2. Edit it → the time box shows the same UK time back; Save without changes → nothing shifts. 3. Your existing test reminder (stored 17:45 UTC): the row and the edit form should now BOTH show 18:45 — the stored value is unchanged, only honestly displayed. 4. Edge: set a time of 00:15 UK in summer → it fires at the right instant (stored as 23:15 UTC the previous day); the list still shows 00:15 on the date you picked. 5. Date-only reminders (no time) are unchanged everywhere. 6. End-to-end: let the timed reminder from step 1 fire (pm-comms-remind, ≤5 min after the time you typed) and confirm the email arrives at the UK time you entered — this also completes T-0510's email test."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - reminders
  - dogfood-finding
attention: null
version: 10
---

## Problem

The reminder form's time field is labelled "(optional, UTC)" and stores raw UTC, but every display (next-fire, list) renders in the viewer's local timezone. Austin entered 18:45 intending UK time; the list showed 19:45 (UK is UTC+1 in summer), and he had to counter-enter 17:45 UTC to get an 18:45 UK fire. Nobody thinks in UTC.

## Design

Keep storage in UTC (schema + scheduler unchanged); convert at the form boundary. The time input accepts the user's local wall-clock time; on save it converts date+time to the UTC date+time of that instant (full-date conversion, so DST on the chosen day is respected, and a near-midnight time correctly shifts the stored date). Editing converts back to local for display. The list row's anchor also renders in local time so what you typed is what you see everywhere.

## Conversation

**2026-07-02 17:47 claude-code:** Run run-20260702-1745 completed — Reminder times now work in UK time, the way you'd expect. Before, the time box quietly wanted UTC: you typed 18:45 meaning UK time, but the list then showed 19:45 because Britain is an hour ahead of UTC in summer — so what you typed and what you saw never matched, and you had to do the mental maths yourself (entering 17:45 to get 18:45). Now you type the time as it reads on your clock, and that same time is what shows everywhere — the list, the edit form, and when it actually fires. Under the hood it's still stored in the universal format the scheduler needs (including handling the clocks-change days and times near midnight correctly), so nothing about the sending machinery changed. If we'd left it, every timed reminder would risk firing an hour off or forcing UTC arithmetic on whoever sets it. Benefit: what you type is what you get.

---

**2026-07-02 18:44 — you**

done
