---
id: T-0416
title: Venue Database — enhancement ideas (meeting backlog)
type: feature
state: triaged
created: 2026-06-18T08:14:32Z
updated: 2026-06-18T08:14:32Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zac Leslie
  channel: email
  contact: zac@yahire.com
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - venue-database
  - sales
  - transport
attention: null
version: 1
---

## Context

Output of a venue-database meeting (Zac, Nath, Terry, Rob). A list of ideas to improve the venue database across ops, transport and sales. Ben's steer: "needs time to unpack when the roadmap allows — highlight quick wins / most important." Captured as one backlog ticket; individual items to be split out as they're prioritised.

## General

- Mark **"mandatory questions"** per venue — venues needing extra client questions (e.g. loading-bay booking like Westfield).
- Driver **"report venue info"** button → raises a ticket to Logistics to approve/amend → saved to the database.
- **Recent complaints** section per venue.

## Transport

- Run-planner prompt: "this is a database venue".
- Driver "my runs" prompt: "this is a database venue".
- **Venue time multiplier** for ETAs (e.g. +1h for queuing/access).

## Sales

- Quick access to **recent setup photos** to share with clients. *(Zac flagged a possible GDPR issue — check before building.)*

## Out-there

- View of **most-frequented venues** (by number of contracts, value, transport issues).
- **"Hide from driver view"** toggle — drivers don't need all DB info.

## Website-facing (noted — likely belong to Websites/yasite, not this ticket's build scope)

- Surface certain venue rooms/access to clients on the website (e.g. "there are 4 function rooms — which room?").
- Customer-facing **floor-plan splitter** — split the items into the rooms of the venue.

## Internal process (non-dev — noted for context)

- Sales: a more formal process for reading the database so the right questions are asked, not just account managers remembering.
- Logistics: ask supervisors what else we need from the database.
- Rob & Zac: brainstorm geographical DU.
- Sales: introduce themselves to key venue staff (e.g. the loading-bay staff) — they affect customer feedback.

## Next

- Backlog — needs unpacking + prioritisation; identify quick wins (Ben).
- GDPR check required before sharing setup photos externally.