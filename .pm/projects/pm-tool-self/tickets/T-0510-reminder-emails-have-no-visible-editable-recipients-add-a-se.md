---
id: T-0510
title: Reminder emails have no visible/editable recipients — add a Send-to editor
type: bug
state: in_progress
created: 2026-07-02T17:32:38Z
updated: 2026-07-02T17:32:52Z
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
  - "The reminder create/edit form shows a Send-to section when 'Also send email' is ticked: add a recipient by name+email, remove with one click"
  - Recipients persist as the reminder's stakeholders (channel email) and survive edit round-trips
  - Email ticked with zero recipients shows an inline warning that nobody will be emailed
  - The reminders list row shows the recipients so who-gets-it is visible without opening Edit
out_of_scope: []
code_anchors:
  - path: web/app/reminders/RemindersManager.tsx
    symbol: ReminderForm / toReminderInput
  - path: web/app/reminders/page.tsx
    symbol: ReminderRow mapping
  - path: web/app/_actions/reminders.ts
    symbol: create/updateReminder (already accept stakeholders — no change)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1732
    started: 2026-07-02T17:32:52Z
    status: in_progress
labels:
  - reminders
  - dogfood-finding
attention: null
version: 3
---

## Problem

A global reminder's email goes to the stakeholders stored ON the reminder — but the web form (create AND edit) has no way to see or set them. The "Also send email" checkbox therefore arms a channel with zero recipients: dispatch resolves nobody and silently sends nothing. Austin: "i cant see any stakeholder on the global reminder" (screenshot of the RM-0002 edit form — no recipients section).

## Design

- ReminderForm gains a "Send to" section (shown when email is ticked): recipients as name+email chips with add/remove; stored as reminder stakeholders `{name, channel: "email", contact}` (empty notify_on already matches reminder_due by default).
- Warn inline when email is on but no recipients are set.
- The reminder list row shows who gets the email.
- Backend already supports it — createReminder/updateReminder accept `stakeholders`; this is UI-only.
