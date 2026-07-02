---
id: T-0510
title: Reminder emails have no visible/editable recipients — add a Send-to editor
type: bug
state: review
created: 2026-07-02T17:32:38Z
updated: 2026-07-02T17:35:53Z
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
    status: completed
    ended: 2026-07-02T17:35:53Z
    summary: "Reminder emails now have visible, editable recipients. Before, ticking \"Also send email\" on a reminder looked like it worked but actually emailed no one — the recipients live on the reminder itself, and the form gave no way to see or add them, so the email quietly went nowhere. Now, ticking the email option opens a \"Send to\" box where you add people by name and email address; each person shows as a removable chip, and the reminder's row in the list shows who will be emailed without opening it. If email is on but nobody is added, both the form and the list show a clear warning that no one will receive it. If we'd left it, certificate-renewal-style reminders would appear to be safeguarding things while never actually emailing anyone. Benefit: you can see and control exactly who gets each reminder email, and a mis-configured one announces itself."
    test_plan: "1. After deploy, open /reminders and Edit the existing \"Yahire Website Certificate\" / RM-0002 reminder. Tick \"Also send email\" → a Send-to box appears with a warning that there are no recipients. 2. Add yourself (name optional, email required — Enter or Add commits; invalid emails keep Add disabled; adding the same address twice doesn't duplicate). Save changes → the row now shows the mail icon with your name; hover shows the full address. 3. Refresh — recipients persist; re-open Edit — chips are pre-filled; remove one and save — it stays removed. 4. A reminder with email ON and no recipients shows \"email — no recipients\" in warning colour on the row. 5. END-TO-END EMAIL: create a test reminder anchored today with Time set ~10 min ahead (UTC!), offset \"On the day\", email + a recipient = you. Within ~5 min of the fire time the pm-comms-remind timer sends it — check your inbox for \"Reminder: <title>\" and the reminder's reminders_sent ledger gains 0d@<date>. (Or trigger immediately on the box: sudo systemctl start pm-comms-remind.service.) 6. Regression: dashboard-only reminders unaffected; create-form day checkboxes (T-0504) still work; unticking email keeps previously-saved recipients (hidden, not deleted)."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - reminders
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T17:35:53Z
version: 4
---

## Problem

A global reminder's email goes to the stakeholders stored ON the reminder — but the web form (create AND edit) has no way to see or set them. The "Also send email" checkbox therefore arms a channel with zero recipients: dispatch resolves nobody and silently sends nothing. Austin: "i cant see any stakeholder on the global reminder" (screenshot of the RM-0002 edit form — no recipients section).

## Design

- ReminderForm gains a "Send to" section (shown when email is ticked): recipients as name+email chips with add/remove; stored as reminder stakeholders `{name, channel: "email", contact}` (empty notify_on already matches reminder_due by default).
- Warn inline when email is on but no recipients are set.
- The reminder list row shows who gets the email.
- Backend already supports it — createReminder/updateReminder accept `stakeholders`; this is UI-only.

## Conversation

**2026-07-02 17:35 claude-code:** Run run-20260702-1732 completed — Reminder emails now have visible, editable recipients. Before, ticking "Also send email" on a reminder looked like it worked but actually emailed no one — the recipients live on the reminder itself, and the form gave no way to see or add them, so the email quietly went nowhere. Now, ticking the email option opens a "Send to" box where you add people by name and email address; each person shows as a removable chip, and the reminder's row in the list shows who will be emailed without opening it. If email is on but nobody is added, both the form and the list show a clear warning that no one will receive it. If we'd left it, certificate-renewal-style reminders would appear to be safeguarding things while never actually emailing anyone. Benefit: you can see and control exactly who gets each reminder email, and a mis-configured one announces itself.
