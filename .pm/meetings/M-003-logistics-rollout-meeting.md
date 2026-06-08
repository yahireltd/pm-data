---
id: M-003
slug: logistics-rollout-meeting
title: Logistics Rollout meeting followup
state: scheduled
created: 2026-06-04T13:29:31Z
updated: 2026-06-08T12:15:24Z
scheduled_at: 2026-06-15T14:30:00Z
duration_minutes: 30
location: TBD
project: null
organizer:
  kind: human
  name: unknown
stakeholders:
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-04T01:26:11Z
    role: Dev
    notify_on:
      - comment_added
      - state_change
      - meeting_held
      - outcome_recorded
      - assigned
      - meeting_scheduled
  - name: ben
    channel: email
    contact: ben@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:44:37Z
    role: SME
  - name: Rob Sousa
    channel: email
    contact: rob@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:46:23Z
    role: Logistics Manager (Logistics SME)
  - name: Orfield Dash
    channel: email
    contact: orfield@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:47:03Z
    role: Logistics Planner (Logistics SME)
  - name: Nana The Top Plana
    channel: email
    contact: nana@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:47:34Z
    role: Planning SME
  - name: Zac Lesle
    channel: email
    contact: zac@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:48:39Z
    role: Operations Director / Geography Teacher
agenda:
  - topic: To go throught what to test fro the logistics rollout
  - topic: To Take any suggestions on features
outcomes:
  - description: During the testing i noticed some things that were working when i have been testing that were working but didnt work every time when we were testing. (eg the loading of the live plan where there is some data)
    recorded_at: 2026-06-04T13:32:10Z
  - description: Also noticed some supprises when copying the data from one to the other. Not sure if it was just rob using system vs the routingtest url - i dont think it was
    recorded_at: 2026-06-04T13:38:38Z
  - description: Logistics were left with a list of specific things to test and report back on and agreed to spend some time on this
    recorded_at: 2026-06-05T15:18:50Z
attachments: []
calendar:
  graph_event_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAALqCQ1nAACnphZ2BstsS50BbU5pWRaYAAdKEE4zAAA=
  ics_url: null
  organizer_mailbox: support@yahire.com
kind: uat
suggested_features:
  - feature: Do not show c1 / d1 on contracts that get copied to the run planner
    ticket: T-0217
  - feature: Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date
    ticket: T-0218
  - feature: Forcing the close of the run planner tab is overkill - we should be able to just take over without them closing it  and show the sketch planner active message on the run planner when sketch takes over
    ticket: T-0219
  - feature: Do not load today as the default date on sketch planner
    ticket: T-0220
  - feature: look into half luton splitting - potential better way to split if they get re-combined anyway on finalise.
    ticket: T-0221
  - feature: vehicles used more than once should be distinguished with some icon / colour tone.
    ticket: T-0222
  - feature: When finalisng can we sort the sketch planner the same way we sort the run planner on load- also introduce a sort runs on the sketch planner
    ticket: T-0223
reminders: []
---

# Logistics Rollout meeting

## Pre-meeting notes

_After meeting rob / orfield / nana and giving the tesing plan to them follow up the meeting to record progress and capture feedback. _

## Minutes

_Filled during/after._
