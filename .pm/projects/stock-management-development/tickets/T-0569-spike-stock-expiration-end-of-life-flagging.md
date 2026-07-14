---
id: T-0569
title: "Spike: stock expiration / end-of-life flagging"
type: spike
state: triaged
created: 2026-07-14T13:00:36Z
updated: 2026-07-14T13:00:36Z
project: stock-management-development
section: null
parent: null
milestone: MS-005
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: kickoff M-001
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
  - stock
  - quality-management
  - concept
  - parked
attention: null
version: 1
---

## Source
Kickoff M-001 (Ben's document). Flagged as **"a huge project"** — parked pending its own scoping.

## Idea
Flag stock that's past its planned end-of-life — e.g. an item planned to go out ~10–15 times before end-of-life, or a set expiry date. After expiry it doesn't necessarily come off stock, but it **triggers a line check + a decision** on whether to keep hiring the items.

## The hard part (scoping questions)
- Feasible for **small lines** (Paris pouffes, gold birdcages, etc.); very hard for **large mixed-batch lines** where batches are blended (Limewash Chiavari chairs). Which lines are in scope?
- Count uses vs a date, or both?
- Is this a **slice of Stock Management** or **its own project**?

## Not now
Parked in PH-005. Needs a do / drop / spin-off decision before any work.