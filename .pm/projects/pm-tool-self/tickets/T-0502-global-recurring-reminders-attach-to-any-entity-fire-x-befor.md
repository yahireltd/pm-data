---
id: T-0502
title: Global recurring reminders — attach to any entity, fire X-before a date, multiple reminders + repeat, surface on dashboards
type: feature
state: in_progress
created: 2026-07-01T14:16:23Z
updated: 2026-07-01T15:20:29Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A reminder can be attached to any entity (ticket, project, milestone, sprint, meeting, decision, and a free-standing/global reminder not tied to an entity) — not meetings-only as today
  - A reminder targets a date (an explicit due date, or a field on the entity e.g. a renewal/expiry date) and fires a configurable lead time BEFORE that date (e.g. 30d / 7d / 1d before)
  - More than one reminder can be set on the same target (e.g. 30d AND 7d AND 1d before) — a list of offsets, each fired once
  - "Reminders can repeat: a recurrence (e.g. annually for a renewal) so the next cycle's reminders are re-armed automatically after the date passes"
  - Due/upcoming reminders surface on the dashboard (a 'Reminders due' card, soonest-first, across projects), analogous to the Upcoming Meetings card
  - "Firing reuses the ADR-018 pattern: a poller + a sent-ledger keyed to (reminder id, occurrence) so each reminder/occurrence fires exactly once and survives restarts; no duplicate sends"
  - Optional delivery via the existing comms path (email) in addition to the dashboard surface
  - Reminders can be created/edited/dismissed from the web UI and via an MCP tool
out_of_scope: []
code_anchors:
  - path: web/app/_components/RemindersEditor.tsx
    note: Existing meeting-only reminders editor — generalize the model beyond meetings
  - path: web/app/_components/UpcomingMeetings.tsx
    note: Dashboard card pattern to mirror for a 'Reminders due' card
  - path: web/app/_actions/meetings.ts
    symbol: pushToCalendar
    note: Existing reminder/calendar action surface
  - path: comms
    note: Poller + sent-ledger lives in comms (remind.ts) per ADR-018; extend it to generic reminders keyed to (reminder id, occurrence)
relates:
  - T-0197
  - T-0160
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260701-1451
    model: claude-opus-4-8
    started: 2026-07-01T14:51:21Z
    status: in_progress
    progress:
      - at: 2026-07-01T15:20:29Z
        note: "Full end-to-end feature built, typechecked, built, committed + pushed (33b2442). Awaiting deploy (needs web build + service restart — I can't SSH to prod). Slices: (1) schema schemas/reminder.schema.json + global RM-NNNN id in both allocators + .pm/reminders/ paths + cli loader/finder/types. (2) shared pure scheduling cli/src/lib/reminder-schedule.ts (offsets, due-window, recurrence roll-forward, occurrence-keyed fire-once ledger) + 15 unit tests green. (4) MCP tools pm_create/list/get/update_reminder registered; tools-list test updated. (5) comms: reminder_due event + reminder entity kind + template + runReminderEntities poller pass wired into `pm-comms remind` (fires due via dispatch, records ledger, advances recurrence/closes one-shots). (6) web: /reminders page (create/edit/dismiss/delete), 'Reminders due' dashboard card, top-nav Bell entry, types/loader re-export. (7) SCHEMA.md Reminder section + RM- id space (docs-drift OK); ADR-048 filed. Full-repo typecheck clean; web build clean (/reminders route compiled); cli reminder tests 15/15, mcp tools-list + cross-project 10/10, comms 38/38. Pre-existing cli smoke + mcp round-trip failures are the missing-.pm-fixture issue, unrelated."
labels:
  - reminders
  - dashboard
attention: null
version: 5
---

## Problem

Reminders today are **meeting-only** (T-0197, T-0160, ADR-018): you can set reminder offsets on a meeting and the poller sends them. There is no way to set a reminder against anything else — a ticket, a contract/renewal date, a milestone target, or a free-standing "remind me" — and no way to have a reminder **repeat** (e.g. an annual renewal) or set **several** reminders at decreasing lead times before the same date.

Concrete driver: things like **ticket/contract renewals** should appear on dashboards and let you set reminders for X-period-before, with more than one reminder per item.

## What we want

- Attach reminders to **any** entity (or a standalone reminder), targeting a date.
- Fire at one or more **lead-time offsets** before that date (30d / 7d / 1d …).
- **Repeat** for recurring dates (renewals): re-arm the next cycle automatically.
- **Surface on the dashboard** — a "Reminders due" card, soonest-first, across projects (mirror the Upcoming Meetings card).
- Deliver on the dashboard and optionally by email via the existing comms path.

## Approach / context

Build on the meeting-reminder foundation rather than starting fresh:
- **ADR-018** already defines the pattern — per-item reminder config + a **sent-ledger keyed to the scheduled time**, fired by a poller. Generalize the ledger key to **(reminder id, occurrence)** so repeats and multiple offsets each fire exactly once.
- The dashboard card mirrors `UpcomingMeetings.tsx`.
- Needs a generic reminder data model (target entity ref OR standalone, anchor date/field, list of offsets, optional recurrence) — a small design step (likely a new ADR) before implementation.

Relates to T-0197 / T-0160 (meeting reminders) and ADR-018 (reminder architecture).

## Conversation
