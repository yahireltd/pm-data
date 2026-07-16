---
id: M-001
slug: logistics-meeting-route-planner
title: Logistics Meeting Route Planner
state: scheduled
created: 2026-07-08T12:16:32Z
updated: 2026-07-08T12:25:01Z
scheduled_at: 2026-07-16T11:30:00Z
duration_minutes: 60
location: IT Office
project: route-planner-release
pre_project: null
milestone: null
organizer:
  kind: human
  name: unknown
stakeholders:
  - name: Orfield Dash
    channel: email
    contact: orfield@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-08T12:17:05Z
    role: Logistics SME
    notify_on:
      - meeting_scheduled
  - name: Rob Sousa
    channel: email
    contact: rob@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-08T12:17:17Z
    notify_on:
      - meeting_scheduled
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-08T12:17:30Z
    role: Developer
    notify_on:
      - state_change
      - meeting_reminder
      - meeting_scheduled
      - comment_added
      - assigned
      - outcome_recorded
      - meeting_held
agenda:
  - topic: Review the feedback from the logistics users
  - topic: Decide if they are confident enough to start using it on live or are further development or bug fixes are needed
outcomes: []
attachments: []
calendar:
  graph_event_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAALqCQ1nAACnphZ2BstsS50BbU5pWRaYAAdbLqJ6AAA=
  ics_url: null
  organizer_mailbox: support@yahire.com
kind: uat
version: 14
reminders:
  - minutes_before: 1440
    channels:
      - email
  - minutes_before: 60
    channels:
      - email
reminders_sent:
  - 1440@2026-07-16T11:30:00Z
  - 60@2026-07-16T11:30:00Z
---

# Route Planning Meeting / Demo

## What Logistics Needs to Test

####   Workflow basics

  - Build a plan on a clean future date → push → verify it appears in run planner

  - Reset & re-solve a few times, check no jobs lost

  - Lock 2-3 routes → re-solve → confirm locked stops don't move

 

####  On the day / last minute replanning

  - Push a plan to run planner

  - Mark a couple of runs as loaded (log item counts)

  - Open sketch again → confirm loading runs show as auto-locked

  - Re-solve → confirm locked & loading work is preserved; only unlocked work moves

  - Push again → run planner reflects the changes, loaded work untouched

#### Concurrent editing

  - Two users try to open sketch for same date → second blocked with name

  - Sketch open in tab A, try run planner same date → blocked

  - Close tab without releasing → wait 60s → can re-enter

  - Lock a route → verify the lock holds through the auto-poll (10s)

 

#### Safety nets

\- Pick items on a contract (type=1), then push a sketch that moves it to another vehicle → pick log survives

\- Load items on a contract (type=2), try to move it in sketch → warning at finalize ("loaded on different vehicle")

\- Two physically-conflicting locked shifts → see overlap warning

\- Sketch with multi-shift vehicle → no shifts overlap, no warnings

 

Known issues.\
\- Drive time multipliers at certain times of day. These will be tweaked once I have some more info on which are wrong. You will probably notice at certain times of day we are on the early / late side – please give a report when you see these  - I may add in an option to report incorrect drive time which will help me build the data.
