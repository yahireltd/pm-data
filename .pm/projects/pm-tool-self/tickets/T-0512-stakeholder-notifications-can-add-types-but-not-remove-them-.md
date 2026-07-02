---
id: T-0512
title: "Stakeholder notifications: can add types but not remove them — and no way to see or silence the defaults"
type: bug
state: review
created: 2026-07-02T18:39:11Z
updated: 2026-07-02T18:43:48Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Editing a meeting stakeholder shows their EFFECTIVE subscriptions pre-ticked (defaults when none explicitly set)
  - Unticking chips and saving sticks — including unticking everything, which fully silences that person (no default fallback)
  - Adding chips still works; add-dialog behaviour unchanged (no selection = defaults apply)
  - "meeting_reminder is a selectable chip and remains in the empty-default set (T-0357 unchanged: minutes stay opt-in)"
  - "Comms test locks the semantics: absent notify_on → defaults; explicit [] → nothing"
out_of_scope: []
code_anchors:
  - path: web/app/_components/AddStakeholderDialog.tsx
    symbol: edit-mode preselect + always-send
  - path: web/app/_components/StakeholderList.tsx
    symbol: per-scope defaultNotify
  - path: cli/src/lib/stakeholders.ts
    symbol: STANDARD_NOTIFY_EVENTS / patchStakeholder
  - path: comms/src/resolve.ts
    symbol: notifyOnMatches ([] = silence)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1839
    started: 2026-07-02T18:39:24Z
    status: completed
    ended: 2026-07-02T18:43:48Z
    summary: "You can now remove notification types as easily as you add them — and see what someone is actually signed up for. Before, three things conspired against you: the edit window treated \"nothing ticked\" as \"don't change anything\", so unticking your last selection quietly undid itself; the system treated an empty choice the same as no choice, silently re-applying its built-in defaults; and the window showed everything unticked for people running on those defaults, hiding what they really receive. Now the edit window opens showing the person's real, effective subscriptions pre-ticked; whatever you leave ticked when you save is exactly what they get; and unticking everything genuinely switches that person's notifications off. Meeting reminders also became a visible, tickable option rather than an invisible default. Adding someone is unchanged — a new person with no choices just gets the sensible defaults. The decision is recorded as ADR-050. If we'd left it, testing and trusting the minutes-opt-in change would have been impossible, since subscriptions couldn't actually be edited down. Benefit: what the window shows is what people receive, in both directions."
    test_plan: '1. After deploy, open the meeting where you added yourself and click the pencil on your stakeholder entry. The "Notify on" chips should show your EFFECTIVE subscriptions pre-ticked (state_change, meeting_scheduled, meeting_reminder for a defaults-based entry) with a note explaining untick-all = silence. 2. REMOVE one (e.g. meeting_scheduled), Save, reopen — it stays removed (your original bug). 3. Untick EVERYTHING, Save, reopen — all chips stay unticked, and the list row shows no subscription tags; comms-wise that person now receives nothing (verify via a reminder/held event if desired: they get no email). 4. Re-tick meeting_held, Save — they now get minutes (T-0357 opt-in path works end-to-end). 5. ADD a brand-new stakeholder with no chips ticked — they behave as before (defaults: scheduled + reminders, NO minutes). 6. Cross-impact (shared dialog): repeat a quick edit-and-remove on a TICKET stakeholder and a PROJECT stakeholder — same behaviour, defaults pre-tick just state_change there. 7. Automated: bun test comms/ (40 pass, incl. the new absent-vs-empty locking test).'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - comms
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T18:43:48Z
version: 4
---

## Problem

Austin, testing the minutes-opt-in change (T-0357/ADR-049): added himself to a meeting, then couldn't change what he was signed up for — "I can add to the notification types but not remove."

Three compounding causes:
1. The edit dialog sends an empty selection as `undefined` = "no change", so unticking the last chip silently reverts on refresh (add works, remove-to-zero doesn't).
2. `patchStakeholder` deletes `notify_on` when handed an empty list, and empty/absent falls back to the DEFAULT set in comms — so "nothing ticked" can never mean "no notifications".
3. The dialog shows all chips unticked for a defaults-based stakeholder, hiding what they're effectively signed up for (and `meeting_reminder` isn't even a chip, so materialising an explicit list would silently kill their reminders).

## Design

- Semantics: `notify_on` ABSENT = the defaults; explicit EMPTY `[]` = fully silenced. Comms resolve + patchStakeholder updated accordingly (locking test added).
- `meeting_reminder` joins the standard notify events so the default set is fully representable as chips.
- Edit dialog: pre-selects the EFFECTIVE subscriptions (per-scope defaults) when the person has none set, always saves exactly what's ticked (including nothing), and says plainly that unticking everything silences the person. Add dialog unchanged (no selection = defaults).

## Conversation

**2026-07-02 18:43 claude-code:** Run run-20260702-1839 completed — You can now remove notification types as easily as you add them — and see what someone is actually signed up for. Before, three things conspired against you: the edit window treated "nothing ticked" as "don't change anything", so unticking your last selection quietly undid itself; the system treated an empty choice the same as no choice, silently re-applying its built-in defaults; and the window showed everything unticked for people running on those defaults, hiding what they really receive. Now the edit window opens showing the person's real, effective subscriptions pre-ticked; whatever you leave ticked when you save is exactly what they get; and unticking everything genuinely switches that person's notifications off. Meeting reminders also became a visible, tickable option rather than an invisible default. Adding someone is unchanged — a new person with no choices just gets the sensible defaults. The decision is recorded as ADR-050. If we'd left it, testing and trusting the minutes-opt-in change would have been impossible, since subscriptions couldn't actually be edited down. Benefit: what the window shows is what people receive, in both directions.
