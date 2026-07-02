---
id: ADR-049
slug: meeting-minutes-are-opt-in-the-empty-notify-on-default-exclu
title: Meeting minutes are opt-in — the empty-notify_on default excludes held/outcome
state: accepted
decided: 2026-07-02
decided_by:
  - claude-code
  - austin
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0357
version: 1
---

## Context

When a stakeholder has no explicit `notify_on`, `notifyOnMatches` (comms/src/resolve.ts) falls back to a default trigger set. An earlier change had widened that default to include the meeting lifecycle events — `meeting_scheduled, meeting_held, meeting_reminder, outcome_recorded` — on the rationale that "someone on a meeting is an attendee, so they should get its comms without hand-picking notify_on."

In dogfooding this over-notified: a pre-project meeting's minutes (fired as `meeting_held` / `outcome_recorded`) were emailed to the entire stakeholder register, including people who never subscribed to updates (T-0357). A pre-project carries the whole register as stakeholders, so "attendee ⇒ gets minutes" is wrong there.

## Decision

Minutes are **opt-in**. The empty-`notify_on` default is narrowed to:

    ["state_change", "meeting_scheduled", "meeting_reminder", "reminder_due"]

`meeting_held` and `outcome_recorded` (the minutes) are removed from the default — only stakeholders who explicitly subscribe receive them. Both events are already offered in the stakeholder dialog's "Notify on" list (STANDARD_NOTIFY_EVENTS), so subscribing is a UI toggle, no new mechanism needed.

The events kept in the default are the ones an attendee cannot self-subscribe to but should still get: `meeting_scheduled` fires at create-time before attendees exist (the calendar invite is the real "you're scheduled" signal), and `meeting_reminder` (T-0197) has no UI toggle — dropping it would silently kill meeting reminders. `reminder_due` belongs to the RM- reminder entity, not meetings.

## Consequences

- To have someone receive a meeting's minutes, tick `meeting_held` and/or `outcome_recorded` on their stakeholder entry. Absent that, they are on the meeting but not emailed the minutes — the desired default.
- Reverses the earlier "attendee auto-gets-everything" default; a locking test was added (comms/tests/dispatch.test.ts) so it isn't silently widened back.
- No change to explicit subscribers, to ticket/project comms, or to reminders.
