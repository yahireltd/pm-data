---
id: ADR-050
slug: notify-on-semantics-absent-defaults-explicit-empty-fully-sil
title: "notify_on semantics: absent = defaults, explicit empty = fully silenced"
state: accepted
decided: 2026-07-02
decided_by:
  - claude-code
  - austin
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0512
version: 1
---

## Context

ADR-049 made meeting minutes opt-in by narrowing the empty-`notify_on` default set. Testing it exposed a deeper conflation: an empty `notify_on` and an absent one meant the same thing ("defaults apply"), the edit dialog sent an empty selection as `undefined` (= no change), and `patchStakeholder` deleted the field when handed an empty list. Net effect, as Austin reported: "I can add to the notification types but not remove" — unticking the last chip silently reverted, and there was no way to silence someone entirely. The dialog also showed all chips unticked for a defaults-based stakeholder, hiding what they effectively receive.

## Decision

`notify_on` has two distinct states:

- **Absent** (field not set) — "no explicit choice": the scope's DEFAULT set applies (`state_change, meeting_scheduled, meeting_reminder, reminder_due` — minutes stay opt-in per ADR-049).
- **Explicit empty `[]`** — "fully silenced": the person receives nothing. The comms resolver short-circuits, and `patchStakeholder` stores the empty list instead of deleting it.

Supporting changes:
- `meeting_reminder` joins `STANDARD_NOTIFY_EVENTS` so the default set is fully representable as dialog chips — without it, materialising an explicit list would silently kill someone's meeting reminders.
- The edit dialog pre-ticks the EFFECTIVE subscriptions (per-scope defaults) when the person has no explicit choices, and saves exactly what's ticked — including nothing. The add dialog is unchanged: no selection on add stays absent (defaults), avoiding needless list materialisation.

## Consequences

- Removing subscriptions now works symmetrically with adding them; "no notifications for this person" is expressible.
- What the dialog shows is what comms does — no invisible defaults.
- Historic hand-written entries carrying a literal `notify_on: []` (rare — both write paths previously omitted/deleted empties) change meaning from "defaults" to "silenced"; acceptable, and visible in the dialog.
- Locking test in comms/tests/dispatch.test.ts (absent → defaults; [] → nothing).
