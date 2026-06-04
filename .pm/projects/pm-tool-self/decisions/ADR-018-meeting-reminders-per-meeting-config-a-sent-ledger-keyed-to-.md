---
id: ADR-018
slug: meeting-reminders-per-meeting-config-a-sent-ledger-keyed-to-
title: "Meeting reminders: per-meeting config + a sent-ledger keyed to the scheduled time, fired by a poller that mirrors the inbound one"
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0197
---

## Context
Meetings needed automatic reminders ahead of the start time (e.g. 24h and 1h before). The existing comms triggers fire on state transitions (scheduled/held/outcome) — they're event-driven, not time-driven, so they can't express 'fire N minutes before scheduled_at'. We needed a time-based mechanism that (a) sends each reminder at most once, (b) does the right thing when a meeting is rescheduled, and (c) doesn't require a new persistent scheduler. Options: an external scheduler keyed by meeting+offset, or reuse the cron-poller pattern already proven for inbound email.

## Decision
Store reminders declaratively on the meeting (reminders: [{minutes_before, channels}]) plus a reminders_sent ledger, and compute due reminders with a pure, I/O-free function over [now, now+window). The dedupe key is ${minutes_before}@${scheduled_at} — tying a reminder to the exact scheduled time it was computed against, so rescheduling a meeting naturally re-arms its reminders (old keys stop matching) while the ledger still blocks a duplicate send for any (offset, time) pair. A poller mirroring the inbound-email cron walks meetings, sends the due reminders, and appends their keys to the ledger; an in-app notification center is the non-email surface. Editing the reminder set deliberately does not clear the ledger.

## Consequences
Reminders reuse the filesystem-first, cron-poller architecture (no new persistent scheduler), are deterministic and unit-testable (time/window passed in), idempotent via the ledger, and self-correcting across reschedules. Trade-offs: timing precision is bounded by the poll interval and the look-ahead window (a reminder due between ticks must fall inside the window or be missed); the ledger grows on the meeting frontmatter; and at the time of this decision only the config + due-computation (phases 1-2) are landed — the poller and in-app notification center (phase 3) are committed direction, not yet shipped.