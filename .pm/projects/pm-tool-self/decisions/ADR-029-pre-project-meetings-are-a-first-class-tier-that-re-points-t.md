---
id: ADR-029
slug: pre-project-meetings-are-a-first-class-tier-that-re-points-t
title: Pre-project meetings are a first-class tier that re-points to the project on convert
state: accepted
decided: 2026-06-05
decided_by:
  - austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0198
---

## Context

Pre-projects are where an idea is planned before it becomes a real project. Two things were missing that stopped a pre-project from being planned properly: its people were stored as names only (no email or contact), and there was no way to schedule the kickoff or scoping meetings that planning needs. Because the people had no email, you couldn't invite them to anything anyway.

## Decision

We made meetings a first-class part of a pre-project — you can plan them on the pre-project just like on a real project. Under the hood a pre-project meeting is stored in the shared "no-project-yet" meeting area and simply tagged with the pre-project's id, because a pre-project is a single file with nowhere to nest meetings. When the pre-project is converted into a real project, each of its meetings is moved over to that project automatically and the tag is cleared.

We chose this over the simpler alternative of just letting people create loose org-wide meetings and mentally associate them — that would have left the meetings behind when the project was created, losing the kickoff/scoping history.

## Consequences

- A pre-project now has the same stakeholder-and-meeting capability as a real project, and the meeting history survives the move to a real project.
- Convert does one extra step (moving the meetings); if that step hits an error it is handled per-meeting so it can never leave the conversion half-done.
- Pre-project meetings are hidden from the general org-wide meetings list so they don't look like they belong to nobody.