---
id: M-001
slug: logistics-meeting-route-planner
title: Logistics Meeting Route Planner
state: held
created: 2026-07-08T12:16:32Z
updated: 2026-07-16T13:50:25Z
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
  - topic: "Recap — what changed since the test checklist was written: far jobs no longer dropped (was 7/day), drive times recalibrated against Google, breaks fixed and now visible on the board, off-road + hire-vehicle buttons"
    duration_min: 5
    ticket: T-0527
  - topic: "Test results — workflow basics: build on a clean date, push, appears in run planner; reset & re-solve with no lost jobs; locked routes hold through re-solve"
    duration_min: 10
  - topic: "Test results — on-the-day replanning: loaded runs auto-lock in sketch, re-solve preserves locked & loading work, push updates the run planner without touching loaded work"
    duration_min: 8
  - topic: "Test results — concurrent editing: second user blocked with name shown, run planner blocked while sketch open, 60s stale-session release, locks hold through the auto-poll"
    duration_min: 5
  - topic: "Test results — safety nets: pick log survives a vehicle move, loaded-on-different-vehicle warning at finalize, overlapping locked shift warning, manual/item-level splits preserved"
    duration_min: 5
    ticket: T-0505
  - topic: "Drive-time accuracy: where do the plans run early/late now? Walk the Google-vs-solver rows on real runs; do the far runs (Coventry / New Forest) look right since the 8 Jul recalibration? Decide on the report-incorrect-drive-time button"
    duration_min: 8
  - topic: "Bugs and niggles not covered above — incl. contracts skipped for bad postcodes (e.g. the W14 6DS typo): do we want postcode validation at order entry?"
    duration_min: 5
  - topic: "GO / NO-GO — confident to run live? GO: agree start date, first live days, who plans each morning, fallback, week-1 support. NO-GO: written blocking list, each item with an owner and a date, and a re-meet date"
    duration_min: 12
  - topic: "Confirm remaining sprint work is still what logistics wants next: half-luton splitting, inactive-runs copy fix, run-status progress bars"
    duration_min: 2
    ticket: T-0221
outcomes:
  - description: Decided to go live monday need to have a clear rollout plan with safety in mind
    recorded_at: 2026-07-16T12:38:05Z
  - description: Logistics to send me some email templates and how and where they should be triggered
    recorded_at: 2026-07-16T12:39:13Z
  - description: Nana mentioned the original run planner not respecting the job minutes entered by sales - check this isn’t only when it is split and even if it is when split is this the right behaviour
    recorded_at: 2026-07-16T12:40:28Z
  - description: Item lists for split jobs - currently doesnt split on the item level until finalise. We should do this before finalise so it isnt confusing - particularly when loading a live plan that was pre-split from logistics
    recorded_at: 2026-07-16T12:52:02Z
  - description: Full address of job not visible anywhere on the sketch planner
    recorded_at: 2026-07-16T12:52:32Z
  - description: Failed collections to show on the map until they are done. This will allow logistics to see where our routes are passing and potentially add the failed collection onto one
    recorded_at: 2026-07-16T12:53:23Z
attachments: []
calendar:
  graph_event_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAALqCQ1nAACnphZ2BstsS50BbU5pWRaYAAdbLqJ6AAA=
  ics_url: null
  organizer_mailbox: support@yahire.com
kind: uat
version: 26
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

## Update for the meeting (16 Jul) — shipped since the checklist was written

- **Far jobs are no longer dropped** (T-0524/T-0527): the solver was massively over-estimating long drives and abandoning out-of-town jobs (Coventry, New Forest, Chichester) while vans sat spare. The 10 Jul test day went from 7 dropped jobs to 0.
- **Drive times recalibrated against Google actuals** (8 Jul): far legs now predict at or slightly over Google — the "early/late at certain times of day" known issue above should be much reduced. Please still flag anything that looks off; every solve now logs solver-vs-Google per leg for the next calibration pass.
- **Breaks** (T-0530/T-0531/T-0532): long single-run days can take the legal break mid-drive (services stop — shows as a memo on the route); breaks after the final stop and the drive back to depot now SHOW on the board (previously invisible, so route totals looked wrong); the hidden "no breaks before 10:00" rule from February is gone.
- **Fleet buttons**: off-road (maintenance) and hire-vehicle pool buttons live on the sketch; the off-road list shows which vehicles are already used in today's plan.
- **Split preservation** (T-0505/T-0506): manual and item-level splits made by logistics survive re-solve and finalize — the safety-net tests above exercise exactly this.

## Go / no-go framework

**GO** means agreeing, in the meeting: start date; which live days go first (suggest quieter days); who runs the plan each morning; the fallback (run planner works unchanged if a sketch is wrong); and how issues get reported in week 1.
**NO-GO** means a written blocking list — every item gets an owner and a date, and we book the re-meet before leaving the room.
