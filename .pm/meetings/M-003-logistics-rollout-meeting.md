---
id: M-003
slug: logistics-rollout-meeting
title: Logistics Rollout meeting
state: scheduled
created: 2026-06-04T13:29:31Z
updated: 2026-06-04T14:04:01Z
scheduled_at: 2026-06-04T14:30:00Z
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
agenda:
  - topic: To go throught what to test fro the logistics rollout
  - topic: To Take any suggestions on features
outcomes:
  - description: During the testing i noticed some things that were working when i have been testing that were working but didnt work every time when we were testing. (eg the loading of the live plan where there is some data)
    recorded_at: 2026-06-04T13:32:10Z
  - description: Also noticed some supprises when copying the data from one to the other. Not sure if it was just rob using system vs the routingtest url - i dont think it was
    recorded_at: 2026-06-04T13:38:38Z
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: uat
suggested_features:
  - feature: Do not show c1 / d1 on contracts that get copied to the run planner
    ticket: T-0217
  - feature: Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date
  - feature: Forcing the close of the run planner tab is overkill - we should be able to just take over without them closing it  and show the sketch planner active message on the run planner when sketch takes over
  - feature: Do not load today as the default date on sketch planner
  - feature: look into half luton splitting - potential better way to split if they get re-combined anyway on finalise.
  - feature: vehicles used more than once should be distinguished with some icon / colour tone.
  - feature: When finalisng can we sort the sketch planner the same way we sort the run planner on load- also introduce a sort runs on the sketch planner
---

# Logistics Rollout meeting

## Pre-meeting notes

_Agenda is in frontmatter._

## Minutes

_Filled during/after._
