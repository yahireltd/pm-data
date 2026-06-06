---
id: PP-003
slug: demo-neon-smash-arcade-brick-breaker-with-an-online-leaderbo
title: "DEMO — Neon Smash: arcade brick-breaker with an online leaderboard"
state: planning
created: 2026-06-06T00:17:36Z
updated: 2026-06-06T00:19:58Z
stakeholders:
  - name: Austin
    channel: email
    contact: austin@ya-hire.example
    internal: true
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Sponsor & Product Owner — owns scope and acceptance
    notify_on:
      - state:review
      - outcome_recorded
  - name: Sam Patel
    channel: email
    contact: sam@ya-hire.example
    internal: true
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Engineering — dev colleague, reviews agent work
    notify_on:
      - assigned
      - state_change
  - name: Jordan Lee
    channel: email
    contact: jordan@example.com
    internal: false
    added_by:
      kind: agent
      name: claude
    added_at: 2026-06-06T00:19:58Z
    role: Playtester — voice of the player (external)
    notify_on:
      - meeting_held
      - state:done
source_tickets: []
summary: "A small, self-contained arcade game built to demonstrate how we run a project end-to-end in pm-tool. Players bounce a ball off a paddle to smash a wall of neon bricks, chain combos for a high score, and post that score to a global top-10 leaderboard. The game is just the vehicle — the real deliverable is a worked example of the full human↔agent workflow: charter, milestones, decisions, meetings, sprints, and tightly-scoped tickets, run by the course playbook. Labelled DEMO; not a real product."
owner:
  kind: human
  name: Austin
problems:
  - We have no single, end-to-end worked example of a project running through pm-tool — from first idea, through planning, into the build sprints. People learn the workflow piecemeal, by osmosis.
  - "When we show pm-tool to someone, we point at half-finished real projects that need a lot of explaining. There's nothing self-contained to click through that shows the whole shape at once: charter, milestones, decisions, meetings, sprints, scoped tickets, and the human-agent handoff."
  - Agents pick up tickets that are under-specified and then over-build or guess. We have no reference set of 'this is what a properly-scoped ticket looks like.'
goals:
  - Produce a self-contained, clickable example project that shows the whole pm-tool workflow end-to-end.
  - Make it concrete and fun — a real, buildable arcade game (Neon Smash) — so the example engages people instead of being abstract.
  - "Set the quality bar for scoping: every build ticket carries acceptance criteria, code anchors, and design notes an agent could act on cold."
  - "Show the human-agent handoff clearly: the agent claims, records progress, and completes into review; a human verifies and closes."
cost_of_inaction: We keep re-explaining the process by hand at every onboarding and every demo. Scoping quality stays uneven, demos stay vague, and the discipline the tool is meant to enforce looks like overhead instead of obviously paying off — which quietly slows adoption of the tool itself. Each instance is a small cost, but it never stops.
---

# PP-003: DEMO — Neon Smash: arcade brick-breaker with an online leaderboard

## Summary

A small, self-contained arcade game built to demonstrate how we run a project end-to-end in pm-tool. Players bounce a ball off a paddle to smash a wall of neon bricks, chain combos for a high score, and post that score to a global top-10 leaderboard. The game is just the vehicle — the real deliverable is a worked example of the full human↔agent workflow: charter, milestones, decisions, meetings, sprints, and tightly-scoped tickets, run by the course playbook. Labelled DEMO; not a real product.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
