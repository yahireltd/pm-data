---
id: M-005
slug: neon-smash-scoping-design-review
title: Neon Smash — scoping & design review
state: scheduled
created: 2026-06-06T00:21:56Z
updated: 2026-06-06T00:23:04Z
scheduled_at: 2026-06-04T10:00:00Z
duration_minutes: 45
location: Video call
project: demo-neon-smash
pre_project: null
organizer:
  kind: human
  name: Austin
stakeholders: []
agenda:
  - topic: Tech stack & tooling — language, rendering, bundler
    duration_min: 8
  - topic: Architecture — client-authoritative game + thin leaderboard service
    duration_min: 8
  - topic: Leaderboard API contract & failure modes (what happens if it's down?)
    duration_min: 10
  - topic: Score integrity / anti-cheat — how far do we go for a demo?
    duration_min: 8
  - topic: Test strategy — unit, integration, performance (needed?), UAT
    duration_min: 8
  - topic: Environment readiness — repo, one-command run, Claude conventions
    duration_min: 3
outcomes:
  - description: "Stack chosen: TypeScript with an HTML5 Canvas for the game, and a small Bun + SQLite service for the leaderboard, bundled with Vite. Picked for zero heavy dependencies and a one-command local run."
    recorded_at: 2026-06-06T00:23:03Z
    owner:
      kind: agent
      name: Claude
  - description: "Architecture agreed: the browser runs the whole game; the leaderboard is a thin web service the game calls only to submit and fetch top scores. If the service is unreachable, the game keeps playing and shows a friendly 'offline' message."
    recorded_at: 2026-06-06T00:23:03Z
    owner:
      kind: agent
      name: Claude
  - description: "Score integrity: submissions are signed with a one-time code the service hands out, plus a basic sanity check on the server. We accept that a determined cheat can still forge a score — fine for a demo, and recorded as accepted risk."
    recorded_at: 2026-06-06T00:23:03Z
    owner:
      kind: human
      name: Austin
  - description: "Test strategy: unit-test the game rules (bouncing, scoring, combos), integration-test the leaderboard service, skip load/performance testing with a reason (single player, tiny traffic), and do a human play-through before we call it shippable."
    recorded_at: 2026-06-06T00:23:04Z
    owner:
      kind: human
      name: Sam Patel
  - description: "Action: write these decisions up as decision records (ADRs) and a tech-session immediately, so nothing load-bearing lives only in someone's memory."
    recorded_at: 2026-06-06T00:23:04Z
    owner:
      kind: agent
      name: Claude
    due: 2026-06-05
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
---

Design deep-dive before build. Attendees: Austin, Sam Patel, and Claude (drafting the decision records). Goal: lock the load-bearing technical decisions and write them down as decision records (ADRs) plus a tech-session, so a fresh agent conversation can act without re-deriving them. Following the course rule: don't transcribe — capture decisions and next actions.