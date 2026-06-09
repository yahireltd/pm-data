---
id: TS-016
slug: concurrency-safety-build-race-safe-ids-lost-edit-protection-
title: Concurrency safety build — race-safe ids + lost-edit protection (SPR-029)
project: pm-tool-self
created: 2026-06-09T22:06:53Z
updated: 2026-06-09T22:07:36Z
decisions:
  - All id numbering — global and per-project, on every surface including the email-to-ticket path — goes through one shared allocator behind a short cross-process lock with saved counters (ADR-038). Shipped in small slices, each proven by a many-processes-at-once test before deploy.
  - "Lost-edit protection is optimistic versioning: every record carries a version number maintained at the two write chokepoints; a save based on an older copy is refused (the form keeps the user's text) rather than silently overwriting. Rolled out shadow-first (log only, zero false alarms all day) then enforced. A live agent-vs-human race test caught a real hole — records never saved since the feature shipped carried no version, so pages now claim version 0 for them — and the retest passed end-to-end in production."
actions:
  - text: When a status-note body editor ships in the web app, wire it to the already-conflict-ready save (the action accepts the version claim; there is just no edit surface yet).
    ticket: T-0317
side_quests:
  - The command-line and agent-server copies of the file-writing/id libraries are near-duplicates (a parity risk noted in ADR-038) — unify them, and remove the old per-entity allocator helpers that now have no callers.
problem: "Several people and AI agents now use the tool at the same time, and the data layer had no protection: two simultaneous creates could be handed the same id (this had really happened — a meeting id collision overwrote real data), and two people editing the same record would silently wipe each other's changes with no warning."
success_criterion: Concurrent creates always get distinct ids, and a save based on an out-of-date copy is refused with the user's text preserved — proven by automated concurrency tests and a live agent-vs-human race.
phase: build
version: 3
---

# TS-016: Concurrency safety build — race-safe ids + lost-edit protection (SPR-029)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
