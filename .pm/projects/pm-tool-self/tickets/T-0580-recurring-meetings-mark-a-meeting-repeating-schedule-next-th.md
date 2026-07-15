---
id: T-0580
title: "Recurring meetings: mark a meeting repeating + schedule-next that copies agenda & stakeholders (not outcomes)"
type: feature
state: review
created: 2026-07-15T11:13:06Z
updated: 2026-07-15T15:36:13Z
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
  - A meeting can be marked repeating (weekly / every 2 weeks / monthly) from its header, and un-marked
  - "A repeating meeting offers 'Schedule next meeting': creates a NEW meeting one interval later copying agenda, stakeholders, reminders config and setup — but NOT outcomes, minutes, or attachments"
  - Each occurrence records its own outcomes/minutes independently
  - "The chain is navigable: the source shows a link to the next occurrence (and the new one links back); schedule-next can't create duplicates"
  - Works for org-level meetings (M-010) and project meetings alike
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
    symbol: setMeetingRecurrence / scheduleNextOccurrence
  - path: web/app/_components/MeetingDetailHeader.tsx
    symbol: Repeats select + Schedule next
  - path: schemas/meeting.schema.json
    symbol: recurrence / follows / next
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1113
    started: 2026-07-15T11:13:20Z
    status: completed
    ended: 2026-07-15T15:26:17Z
    summary: "Recurring meetings now schedule safely — the double-click trap from your first live test is fixed in all three places it failed. First, feedback: the \"Schedule next meeting\" button now switches to \"Scheduling…\" and locks while it works, so there's a clear visual cue and a second click is impossible. Second, even if two requests somehow race in together, the server now double-checks before linking: the loser deletes its own copy and reports \"already scheduled\" instead of creating a second meeting. Third, the mess you were left with heals itself: a meeting whose next-occurrence link points at something deleted (your M-010 → M-011 situation) simply clears the dead link and schedules normally on the next click — no manual repair needed. And deleting a meeting now tidies the chain in both directions (web and agent delete alike), so ghosts can't be created again. After deploy, just open M-010 and click Schedule next once — it will work."
    test_plan: "1. After deploy, open M-010 (still linked to the deleted M-011): click \"Schedule next meeting\" ONCE → button shows \"Scheduling…\", then you land on the fresh next occurrence with agenda + attendees copied; M-010 shows the new \"Next:\" link. The dead M-011 link healed itself. 2. Rapid-fire test: on a repeating meeting without a next, double-click the button as fast as you can → exactly ONE next occurrence exists afterwards. 3. Delete a next occurrence → the previous meeting's \"Next:\" link disappears and its Schedule button returns (no ghost). 4. Regression: the Repeats selector, follows/next chain links, per-occurrence outcomes, and series_context (T-0592) all behave as before."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T15:26:17Z
version: 7
---

## Problem

M-010 is a weekly global meeting, but the tool has no recurrence: each week either the same meeting gets reused (outcomes pile up across weeks, minutes blur together) or someone hand-recreates it (agenda + stakeholders re-typed). Austin's design: each occurrence is its OWN meeting with its own outcomes/minutes; a "make it repeating" control plus, at the end of a meeting, a "schedule next" button that copies the setup (agenda, stakeholders — and reminders config) but never the outcomes.

## Design

- Meeting gains `recurrence` ({unit: day|week|month, interval} | null) plus `follows`/`next` links so the series is a chain of individual meetings.
- Header UI: a "Repeats" select (none / weekly / every 2 weeks / monthly), and — when repeating — a "Schedule next meeting" button; once the next exists the button becomes a link to it (no accidental duplicates). A "follows" chip links back up the chain.
- scheduleNextOccurrence: new meeting at scheduled_at + interval; copies title, kind, duration, location, organizer, project/pre-project, milestone, stakeholders, agenda, reminder config, and the recurrence itself; fresh body/outcomes/attachments.

## Conversation

**2026-07-15 11:17 claude-code:** Run run-20260715-1113 completed — Your weekly meeting can now actually repeat — with each week keeping its own record. A meeting's header has a new "Repeats" choice (one-off, weekly, every 2 weeks, monthly). Once a meeting repeats, a "Schedule next meeting" button appears — it turns prominent after you mark the meeting held, matching the end-of-meeting moment you described. Pressing it creates next week's meeting in one click: same title, time slot one interval later, same attendees, the same standing agenda, and the same reminder set-up — but a clean slate for outcomes and minutes, because those belong to each individual meeting. The two meetings link to each other ("Next: M-0xx" forward, a back-arrow to the previous one), so the whole series is walkable week by week, and you can't accidentally create two next-weeks — once the next exists the button becomes the link to it. If we'd left it, M-010 would keep accumulating every week's outcomes in one blur or need re-typing weekly. Benefit: one click at the end of each meeting keeps the series going with clean per-week records.

**2026-07-15 15:26 claude-code:** Follow-up fixes from Austin's first live use (commit e2f150d). What went wrong: clicking "Schedule next" gave no visual feedback, so a second click raced the first — M-011 AND M-012 were both created; deleting them left M-010 pointing at a ghost next-occurrence, which blocked scheduling entirely. Fixed three ways: the button now shows "Scheduling…" and locks while working; the server double-checks after creating and rolls back the loser if two requests race; and a dead next-link self-heals — plus deletes now tidy the chain in both directions so ghosts can't happen again. After the next deploy, one click on M-010 schedules next week normally.

**2026-07-15 15:36 claude-code:** Second follow-up (commit 285413f): after deploying the first round, M-010 still showed the dead "Next: M-011" link. Cause: the self-repair ran inside the Schedule action — but the page hides the Schedule button whenever a next-link exists, so with a dead link there was nothing to click and the repair could never run. The meeting page now checks that the linked next/previous meeting actually EXISTS before showing it: a dead link is treated as "not scheduled yet", the Schedule button comes back, and the first click repairs the stored link and schedules normally. Re-test after deploying: open M-010 — the "Next: M-011" text should be GONE and "Schedule next meeting" back; click it once → next week's meeting is created and linked.
