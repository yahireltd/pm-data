---
id: T-0580
title: "Recurring meetings: mark a meeting repeating + schedule-next that copies agenda & stakeholders (not outcomes)"
type: feature
state: review
created: 2026-07-15T11:13:06Z
updated: 2026-07-15T11:17:51Z
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
    ended: 2026-07-15T11:17:51Z
    summary: "Your weekly meeting can now actually repeat — with each week keeping its own record. A meeting's header has a new \"Repeats\" choice (one-off, weekly, every 2 weeks, monthly). Once a meeting repeats, a \"Schedule next meeting\" button appears — it turns prominent after you mark the meeting held, matching the end-of-meeting moment you described. Pressing it creates next week's meeting in one click: same title, time slot one interval later, same attendees, the same standing agenda, and the same reminder set-up — but a clean slate for outcomes and minutes, because those belong to each individual meeting. The two meetings link to each other (\"Next: M-0xx\" forward, a back-arrow to the previous one), so the whole series is walkable week by week, and you can't accidentally create two next-weeks — once the next exists the button becomes the link to it. If we'd left it, M-010 would keep accumulating every week's outcomes in one blur or need re-typing weekly. Benefit: one click at the end of each meeting keeps the series going with clean per-week records."
    test_plan: "1. After deploy, open M-010. In the header set Repeats to \"weekly\". 2. Click \"Schedule next meeting\" → you land on a NEW meeting scheduled exactly a week later, with M-010's attendees and agenda copied, EMPTY outcomes/minutes/attachments, and a back-link to M-010; M-010 now shows \"Next: M-0xx\". 3. On M-010, confirm the schedule-next button is gone (replaced by the Next link) — clicking schedule twice is impossible. 4. Mark the NEW meeting held (confirm dialog) → the schedule-next button on it turns primary; click it → week 3 appears, chain continues. 5. Record an outcome on week 2 → week 3 does NOT inherit it. 6. Set Repeats back to \"One-off\" on any occurrence → its schedule-next button disappears. 7. Regression: a project meeting behaves identically (links stay inside the project); reminders config copied but no reminder fires early (ledger not copied); calendar push/availability/minutes emails unchanged."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T11:17:51Z
version: 4
---

## Problem

M-010 is a weekly global meeting, but the tool has no recurrence: each week either the same meeting gets reused (outcomes pile up across weeks, minutes blur together) or someone hand-recreates it (agenda + stakeholders re-typed). Austin's design: each occurrence is its OWN meeting with its own outcomes/minutes; a "make it repeating" control plus, at the end of a meeting, a "schedule next" button that copies the setup (agenda, stakeholders — and reminders config) but never the outcomes.

## Design

- Meeting gains `recurrence` ({unit: day|week|month, interval} | null) plus `follows`/`next` links so the series is a chain of individual meetings.
- Header UI: a "Repeats" select (none / weekly / every 2 weeks / monthly), and — when repeating — a "Schedule next meeting" button; once the next exists the button becomes a link to it (no accidental duplicates). A "follows" chip links back up the chain.
- scheduleNextOccurrence: new meeting at scheduled_at + interval; copies title, kind, duration, location, organizer, project/pre-project, milestone, stakeholders, agenda, reminder config, and the recurrence itself; fresh body/outcomes/attachments.

## Conversation

**2026-07-15 11:17 claude-code:** Run run-20260715-1113 completed — Your weekly meeting can now actually repeat — with each week keeping its own record. A meeting's header has a new "Repeats" choice (one-off, weekly, every 2 weeks, monthly). Once a meeting repeats, a "Schedule next meeting" button appears — it turns prominent after you mark the meeting held, matching the end-of-meeting moment you described. Pressing it creates next week's meeting in one click: same title, time slot one interval later, same attendees, the same standing agenda, and the same reminder set-up — but a clean slate for outcomes and minutes, because those belong to each individual meeting. The two meetings link to each other ("Next: M-0xx" forward, a back-arrow to the previous one), so the whole series is walkable week by week, and you can't accidentally create two next-weeks — once the next exists the button becomes the link to it. If we'd left it, M-010 would keep accumulating every week's outcomes in one blur or need re-typing weekly. Benefit: one click at the end of each meeting keeps the series going with clean per-week records.
