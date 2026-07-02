---
id: T-0502
title: Global recurring reminders — attach to any entity, fire X-before a date, multiple reminders + repeat, surface on dashboards
type: feature
state: done
created: 2026-07-01T14:16:23Z
updated: 2026-07-02T18:56:26Z
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
  - "[x] A reminder can be attached to any entity (ticket, project, milestone, sprint, meeting, decision, and a free-standing/global reminder not tied to an entity) — not meetings-only as today"
  - "[x] A reminder targets a date (an explicit due date, or a field on the entity e.g. a renewal/expiry date) and fires a configurable lead time BEFORE that date (e.g. 30d / 7d / 1d before)"
  - "[x] More than one reminder can be set on the same target (e.g. 30d AND 7d AND 1d before) — a list of offsets, each fired once"
  - "[x] Reminders can repeat: a recurrence (e.g. annually for a renewal) so the next cycle's reminders are re-armed automatically after the date passes"
  - "[x] Due/upcoming reminders surface on the dashboard (a 'Reminders due' card, soonest-first, across projects), analogous to the Upcoming Meetings card"
  - "[x] Firing reuses the ADR-018 pattern: a poller + a sent-ledger keyed to (reminder id, occurrence) so each reminder/occurrence fires exactly once and survives restarts; no duplicate sends"
  - "[x] Optional delivery via the existing comms path (email) in addition to the dashboard surface"
  - "[x] Reminders can be created/edited/dismissed from the web UI and via an MCP tool"
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
    status: completed
    progress:
      - at: 2026-07-01T15:20:29Z
        note: "Full end-to-end feature built, typechecked, built, committed + pushed (33b2442). Awaiting deploy (needs web build + service restart — I can't SSH to prod). Slices: (1) schema schemas/reminder.schema.json + global RM-NNNN id in both allocators + .pm/reminders/ paths + cli loader/finder/types. (2) shared pure scheduling cli/src/lib/reminder-schedule.ts (offsets, due-window, recurrence roll-forward, occurrence-keyed fire-once ledger) + 15 unit tests green. (4) MCP tools pm_create/list/get/update_reminder registered; tools-list test updated. (5) comms: reminder_due event + reminder entity kind + template + runReminderEntities poller pass wired into `pm-comms remind` (fires due via dispatch, records ledger, advances recurrence/closes one-shots). (6) web: /reminders page (create/edit/dismiss/delete), 'Reminders due' dashboard card, top-nav Bell entry, types/loader re-export. (7) SCHEMA.md Reminder section + RM- id space (docs-drift OK); ADR-048 filed. Full-repo typecheck clean; web build clean (/reminders route compiled); cli reminder tests 15/15, mcp tools-list + cross-project 10/10, comms 38/38. Pre-existing cli smoke + mcp round-trip failures are the missing-.pm-fixture issue, unrelated."
    ended: 2026-07-01T15:33:52Z
    summary: |-
      **What we did** — Added reminders to the tool. You can now set a reminder against anything — a ticket, a project, a renewal date, or a standalone "remind me" — tell it to nudge you at several points before the date (say 30, 7, and 1 day before), and have it repeat (e.g. every year for a renewal). Due reminders show up on the dashboard in a new "Reminders due" card and on a dedicated Reminders page, and can optionally also email the people you list. It's built, deployed, and reachable from the top-nav "Reminders" entry.

      **Why** — Reminders previously only existed for meetings. There was no way to be reminded about a contract/certificate renewal, a ticket that needs chasing, or any dated thing — and no way to be nudged more than once or on a repeating cycle. Renewals and deadlines were relying on someone remembering.

      **What would have happened if we did nothing** — Renewals, expiries and follow-ups would keep depending on memory and ad-hoc notes, so things would occasionally slip — exactly the kind of quiet miss that's expensive when it's a lapsed contract or certificate.

      **The benefit** — Anything with a date can now nudge you ahead of time, as many times as you want, on repeat, visible the moment you open the tool. Agents can create and manage them too. Delivered as a first-class, reusable capability (recorded as ADR-048) rather than a one-off.
    test_plan: |-
      ## Status
      Built, typechecked, unit-tested, web-built, committed (33b2442), pushed, and DEPLOYED to support.yahire.com. The one thing NOT done from the agent session: the live click-through / MCP smoke test — my chat's MCP connection predated the pm-mcp restart, so the 4 new reminder tools weren't callable from it (new MCP tools need a client reconnect; a session restart fixes it). So the live verification below is the reviewer's to run.

      ## Automated (already green)
      - `cd cli && bun test tests/reminder-schedule.test.ts` → 15/15 (offsets, due-window, recurrence roll-forward, fire-once ledger, calendar math).
      - `cd mcp-server && bun test tests/tools-list.test.ts tests/cross-project-id.test.ts` → 10/10 (the 4 reminder tools are advertised).
      - `cd comms && bun test` → 38/38. Full-repo `bun run typecheck` clean; `web` production build clean (/reminders route compiled).
      - Known non-issue: cli `smoke.test.ts` + mcp `round-trip.test.ts` fail in this code checkout for lack of a seed `.pm/` fixture — pre-existing, unrelated.

      ## Live verification (please run)
      ### Web
      1. Top nav → **Reminders** (Bell) → **New reminder**: title, a date ~2–3 weeks out, offsets `30d, 7d, 1d`, Repeat = every year, optionally tick email → **Create**.
      2. It appears in the list with a "next" fire time; the **dashboard** shows it under **"Reminders due"** (soonest-first; overdue ones flag amber).
      3. **Dismiss** hides it from the card; **Reactivate** brings it back; **Delete** removes it.
      4. Attach one to a ticket (target type=ticket, id=T-XXXX) → the card row links to that ticket.

      ### MCP (after reconnecting so the tools load)
      - `pm_create_reminder {title, anchor_date, offsets:["30d","7d","1d"], recurrence:{unit:"year",interval:1}, target:{type:"ticket", id:"T-0261"}}` → returns RM-000N.
      - `pm_get_reminder {id}` → shows next_fire = the soonest upcoming offset; `pm_list_reminders` → soonest-first.
      - `pm_update_reminder {id, state:"dismissed"}` then `{state:"active"}` → toggles; `{offsets:[...]}` edits.

      ### Email + recurrence (background, verify over time)
      - With `email` in channels + stakeholders set, the `pm-comms-remind` timer (5-min) fires a `reminder_due` email at each offset's time and records it in `reminders_sent` (never double-sends).
      - After an anchor date passes: a recurring reminder auto-advances to next year (re-arms); a one-shot flips to `done` and drops off the card.

      ## Cross-impact
      - New global id space `RM-` in both id allocators — existing ids untouched (counter seeded from a dir scan).
      - comms `dispatch()`/`readEntity` gained a `reminder` entity kind + `reminder_due` event; meeting reminders are unchanged (separate path). The `pm-comms remind` tick now runs both passes.
      - Web dashboard added a section between Upcoming meetings and Product backlog; nothing else on the dashboard changed.

      ## Known limitation (documented in ADR-048)
      - Month/year recurrence uses JS UTC calendar math, so month-overflow dates behave like JS (e.g. Jan-31 + 1 month → Mar-03). Fine for annual/monthly/weekly/daily renewals.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: SCHEMA.md — new 'Reminder (RM-NNNN)' section + RM- in the ID allocation list. Design recorded as ADR-048 (pm-tool-self).
labels:
  - reminders
  - dashboard
attention: null
version: 16
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

**2026-07-01 15:33 claude-code:** Run run-20260701-1451 completed — **What we did** — Added reminders to the tool. You can now set a reminder against anything — a ticket, a project, a renewal date, or a standalone "remind me" — tell it to nudge you at several points before the date (say 30, 7, and 1 day before), and have it repeat (e.g. every year for a renewal). Due reminders show up on the dashboard in a new "Reminders due" card and on a dedicated Reminders page, and can optionally also email the people you list. It's built, deployed, and reachable from the top-nav "Reminders" entry.

**Why** — Reminders previously only existed for meetings. There was no way to be reminded about a contract/certificate renewal, a ticket that needs chasing, or any dated thing — and no way to be nudged more than once or on a repeating cycle. Renewals and deadlines were relying on someone remembering.

**What would have happened if we did nothing** — Renewals, expiries and follow-ups would keep depending on memory and ad-hoc notes, so things would occasionally slip — exactly the kind of quiet miss that's expensive when it's a lapsed contract or certificate.

**The benefit** — Anything with a date can now nudge you ahead of time, as many times as you want, on repeat, visible the moment you open the tool. Agents can create and manage them too. Delivered as a first-class, reusable capability (recorded as ADR-048) rather than a one-off.

---

**2026-07-02 18:56 — you**

done
