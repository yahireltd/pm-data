---
id: ADR-048
slug: reminders-are-a-first-class-global-entity-with-an-occurrence
title: Reminders are a first-class global entity with an occurrence-keyed ledger and scheduler-driven recurrence roll-forward
state: accepted
decided: 2026-07-01
decided_by:
  - Austin
  - Claude (Opus 4.8)
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0502
version: 1
---

## Context

Reminders were meeting-only (embedded `reminders[]` on a meeting, fired by the comms poller — ADR-018). T-0502 needed reminders that (a) attach to any entity OR stand alone, (b) fire multiple lead-time offsets before a date, (c) repeat (renewals), and (d) surface on dashboards. Two shapes were considered: embed `reminders[]` on every entity type, or a new first-class entity.

## Decision

A **first-class `Reminder` entity** with a **global id (`RM-NNNN`)**, stored in `.pm/reminders/` (schema: `schemas/reminder.schema.json`).

- **First-class, not embedded** — embedding would touch every entity's schema + loader and still can't express a standalone reminder. A Reminder carries an optional `target {type,id,project}` (omit = standalone).
- **Global id, not per-project** — reminders are cross-cutting and a standalone one has no project. Global (like `T-`/`P-`) also sidesteps the cross-project id-ambiguity class we had just fixed in T-0476, so `findReminderPath` needs no disambiguation.
- **Occurrence-keyed ledger** — the fire-once ledger key is `${offset}@${anchor_date}`, generalising the meeting model's `${minutes_before}@${scheduled_at}`. Because the key embeds the occurrence date, **recurrence falls out for free**: once the anchor passes, the scheduler advances `anchor_date` to the next occurrence and the new keys (new date) naturally re-arm; spent keys are pruned so the ledger stays bounded. A one-shot becomes `state: done`.
- **Shared pure logic** — offset/window/recurrence math lives in `cli/src/lib/reminder-schedule.ts` (no I/O, instants passed in), consumed by the comms poller, the web dashboard, and the MCP list/get. Mirrors the meeting `due.ts` purity discipline.
- **Delivery** — `dashboard` is always-on (the `/reminders` page + a dashboard "Reminders due" card compute upcoming firings live); `email` is opt-in and reuses the existing `dispatch()` path (new `reminder_due` event + `reminder` entity kind + template), resolving the reminder's own `stakeholders[]`. The firing/rolling runs on the same `pm-comms remind` 5-min tick as meeting reminders.

## Consequences

- New global id space `RM-` added to both id allocators (cli + mcp), and a new entity kind threaded through cli types/loaders, comms `readEntity`, and the web re-export.
- Dashboard-only reminders never touch the email ledger — they roll on date, so nothing is "silently consumed" off the card before its date.
- Month/year recurrence uses UTC calendar math (`setUTCMonth`/`setUTCFullYear`), which carries JS month-overflow semantics (e.g. Jan-31 + 1 month → Mar-03); documented + tested. Acceptable for the renewal use-cases (annual/monthly/weekly/daily).
- Reminders are not backed up differently from other `.pm/` data (they ride the same data-repo sync). Relates to ADR-018 (meeting reminders), T-0197/T-0160.
