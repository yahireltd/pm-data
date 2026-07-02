---
id: T-0504
title: Edit Reminders
type: feature
state: review
priority: p2
created: 2026-07-02T11:58:30Z
updated: 2026-07-02T18:45:27Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 116736
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Global reminders on /reminders have an Edit button that opens a pre-filled form and saves via updateReminder"
  - "[x] Lead times are picked as day checkboxes (0,1,2,3,4,5,7,14,30) in both create and edit, not a free-text field"
  - "[x] Editing a reminder preserves any non-preset (hours/weeks) offset rather than dropping it"
  - "[x] Meeting reminders already have an inline add/remove editor (RemindersEditor) — no change needed there"
out_of_scope: []
code_anchors:
  - path: web/app/reminders/RemindersManager.tsx
    symbol: ReminderForm / RemindersManager
  - path: web/app/_actions/reminders.ts
    symbol: updateReminder (pre-existing backend)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1222
    started: 2026-07-02T12:22:11Z
    status: completed
    ended: 2026-07-02T12:22:25Z
    summary: "You can now edit a reminder after creating it, and you pick when it fires from tick-boxes instead of typing codes. Before, the Reminders page could only create, dismiss or delete a reminder — if you got the date or the lead times wrong, you had to delete it and start over. Now every reminder has an Edit button that opens the same form pre-filled, so you can fix the title, date, repeat, email option or lead times in place. The \"remind me before\" field used to be a text box where you typed things like \"30d, 7d, 1d\"; it's now a row of day tick-boxes (on the day, 1, 2, 3, 4, 5, 7, 14, 30 days before) that you just check. If we'd left it, reminders would stay awkward to correct and the code-style text box would keep tripping people up. Benefit: reminders are quick to get right and adjust, with no jargon."
    test_plan: "1. Open /reminders. 2. Click \"New reminder\": confirm the \"Remind me before\" field is now a row of day tick-boxes (On the day, 1, 2, 3, 4, 5, 7, 14, 30 days) instead of a text box; 30/7/1 days are pre-ticked. Tick/untick a few, set a title + date, Create — confirm the row shows the chosen lead times (e.g. \"30 days, 7 days, 1 day\"). 3. On any existing reminder, click Edit: the form opens pre-filled with its title/date/time/repeat/email/lead-times. Change the lead times and title, Save changes — confirm the row updates and the change persists after a refresh. 4. Edit a reminder that has a non-day lead time if one exists (e.g. an hours/weeks value): confirm that odd value still shows as a ticked chip and isn't dropped on save. 5. Confirm Dismiss / Reactivate / Delete still work and only one form is open at a time (opening Edit closes the New form). 6. Meeting reminders (a meeting's own reminder editor) are unchanged — still add/remove by minutes."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T12:22:25Z
version: 12
---

No curent way to edit reminders Global or meeting

## Conversation

**2026-07-02 12:22 claude-code:** Run run-20260702-1222 completed — You can now edit a reminder after creating it, and you pick when it fires from tick-boxes instead of typing codes. Before, the Reminders page could only create, dismiss or delete a reminder — if you got the date or the lead times wrong, you had to delete it and start over. Now every reminder has an Edit button that opens the same form pre-filled, so you can fix the title, date, repeat, email option or lead times in place. The "remind me before" field used to be a text box where you typed things like "30d, 7d, 1d"; it's now a row of day tick-boxes (on the day, 1, 2, 3, 4, 5, 7, 14, 30 days before) that you just check. If we'd left it, reminders would stay awkward to correct and the code-style text box would keep tripping people up. Benefit: reminders are quick to get right and adjust, with no jargon.
