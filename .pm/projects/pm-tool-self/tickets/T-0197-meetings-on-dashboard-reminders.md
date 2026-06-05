---
id: T-0197
title: Meetings on dashboard / reminders
type: feature
state: done
priority: p2
created: 2026-06-03T23:59:09Z
updated: 2026-06-05T21:24:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 67584
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] add a section with upcomming meetings (show contact details of attendees too -- seperate functionality needed for contact info of users)"
  - "[x] create a remindders funcitonality with add to calendar"
out_of_scope: []
code_anchors:
  - path: web/app/_components/UpcomingMeetings.tsx
  - path: web/app/page.tsx
  - path: web/app/_components/MeetingDetailHeader.tsx
  - path: web/app/_components/RemindersEditor.tsx
  - path: web/app/_actions/meetings.ts
    symbol: pushToCalendar
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0000
    model: claude-code
    started: 2026-06-04T00:00:45Z
    status: completed
    summary: 'The dashboard now has an "Upcoming meetings" section: the next few scheduled meetings across every project and the org, soonest first, each showing its time, location, which project it belongs to, and the attendees — with their contact details (name, role, and stored email/phone/slack) pulled from the roster, so you can reach people without digging. Reaching the meeting itself, you can already add it to your Microsoft 365 calendar (which sends the invite to the email attendees) and set or send reminders — so the reminders + add-to-calendar part of the ask is handled there. Net effect: meetings are visible the moment you open the tool, and getting them onto a calendar / reminding attendees is one click from the meeting.'
    ended: 2026-06-05T19:31:46Z
    test_plan: "Dashboard: an \"Upcoming meetings\" card lists future scheduled meetings soonest-first, each with time/location/project and the attendees' contact details; clicking a meeting opens it; the card hides when there are none. Meeting page: \"Add to calendar\" creates the Outlook event and invites the email attendees; the reminders editor sets reminder offsets and \"Send reminder\" sends one now. Past/cancelled meetings don't appear on the dashboard card."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

add upcomming meetings to dashboard, Also some reminders functionality - add to calendar as a checkbox

## Conversation

**2026-06-04 01:07 — Austin Pickering**

T-0197

**2026-06-05 19:31 claude-code:** Run run-20260604-0000 completed — The dashboard now has an "Upcoming meetings" section: the next few scheduled meetings across every project and the org, soonest first, each showing its time, location, which project it belongs to, and the attendees — with their contact details (name, role, and stored email/phone/slack) pulled from the roster, so you can reach people without digging. Reaching the meeting itself, you can already add it to your Microsoft 365 calendar (which sends the invite to the email attendees) and set or send reminders — so the reminders + add-to-calendar part of the ask is handled there. Net effect: meetings are visible the moment you open the tool, and getting them onto a calendar / reminding attendees is one click from the meeting.

---

**2026-06-05 19:40 — you**

works fine

**2026-06-05 21:24 claude:** **What we did** — Added an "Upcoming meetings" section to the dashboard that lists the next few scheduled meetings across every project, soonest first, with each meeting's time, location, project, and attendees' contact details. From a meeting you can add it to your calendar (which invites the attendees) and set or send reminders.

**Why** — People needed to see what meetings were coming up the moment they opened the tool, and to reach attendees and get meetings onto a calendar without hunting around.

**What would have happened if we did nothing** — Upcoming meetings would stay hidden, so people would miss them or have to dig through projects to find them and look up who to contact.

**The benefit** — Meetings are now visible at a glance with attendee contact info at hand, and getting one on a calendar or reminding attendees is a single click. This helps anyone coordinating or attending meetings stay on top of their schedule.
