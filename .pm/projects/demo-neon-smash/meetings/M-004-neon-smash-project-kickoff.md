---
id: M-004
slug: neon-smash-project-kickoff
title: Neon Smash — project kickoff
state: scheduled
created: 2026-06-06T00:21:56Z
updated: 2026-06-06T00:32:19Z
scheduled_at: 2026-06-02T09:00:00Z
duration_minutes: 30
location: In person — Ya-Hire office
project: demo-neon-smash
pre_project: null
organizer:
  kind: human
  name: Austin
stakeholders: []
agenda:
  - topic: In scope — what Neon Smash is
    duration_min: 4
  - topic: OUT of scope — the hard boundary (no accounts, no multiplayer, no cloud, no real anti-cheat)
    duration_min: 6
  - topic: Governance — two-week sprints, weekly status note, decisions as ADRs, end-of-sprint demo
    duration_min: 5
  - topic: Workstream ownership — who owns the client, the service, product, and playtest
    duration_min: 5
  - topic: Meeting cadence — only what we actually need
    duration_min: 3
  - topic: Roundtable — anything we haven't talked about?
    duration_min: 5
  - topic: Links — where the charter, ADRs and board live
    duration_min: 2
outcomes:
  - description: Scope agreed as written in the charter; the out-of-scope list (no accounts, no multiplayer, no cloud hosting, no real anti-cheat) is the hard boundary — anything beyond it is a change request, not silent scope.
    recorded_at: 2026-06-06T00:32:14Z
    owner:
      kind: human
      name: Austin
  - description: "Workstream ownership agreed: Claude builds the game client and the leaderboard service under Austin's direction; Austin owns product, scope and acceptance; Jordan and Austin own playtest and acceptance testing."
    recorded_at: 2026-06-06T00:32:15Z
    owner:
      kind: human
      name: Austin
  - description: "Cadence agreed: two-week sprints, a weekly written status note, decisions captured as decision records, and a short demo at the end of each sprint."
    recorded_at: 2026-06-06T00:32:17Z
    owner:
      kind: human
      name: Austin
  - description: "Action: stand up the dev environment and Claude conventions before Sprint 1 — a repo with a one-command local run, plus the charter and decision records pinned so fresh agent conversations land with full context."
    recorded_at: 2026-06-06T00:32:19Z
    owner:
      kind: agent
      name: Claude
    due: 2026-06-08
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: kickoff
---

Kickoff for the Neon Smash demo project. Attendees: Austin (sponsor/PO), Sam Patel (engineering), Jordan Lee (playtester). Purpose: agree the scope and — most importantly — the explicit out-of-scope boundary, the governance cadence, and who owns what, before Sprint 1 starts. The out-of-scope list is the load-bearing item: anything beyond it is a change request, not silent scope.