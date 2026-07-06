---
id: T-0517
title: "Agency shift editing: date fixes, smart end-date default, out-after-in validation, RBAC-gated delete"
type: feature
state: done
created: 2026-07-06T07:58:57Z
updated: 2026-07-06T08:06:14Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Rob Sousa
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Full edit allows changing the shift date backward and to the next day (parity with quick edit); a clock-out can be on a later date than the clock-in."
  - "[x] Entering a clock-out time with a blank date defaults the date to the clock-in date (or +1 day when the out-time is earlier than the in-time), so hours calculate instead of showing 0."
  - "[x] Saving a shift where clock-out is not after clock-in is rejected with a clear message on all three paths (quick edit, full edit, create); create rejects before any row is inserted."
  - "[x] A delete button (with confirm) appears on the clockings list only for holders of the 'Delete Agency Shift' RBAC permission and SuperUsers; others don't see it and get 403 on the action."
  - "[x] Deleting a shift writes exactly one entry to agencyclockings_log marked 'Deleted by <username>' with the full shift snapshot."
  - "[x] The four action icons fit on a single row in the list."
  - "[x] Employee clock-in/kiosk flow and computeMinutesWorked are unchanged."
  - "[x] Changed PHP files pass php -l."
out_of_scope: []
code_anchors:
  - path: backend/controllers/AgencyController.php
    note: resolveClockOutDate()/prepareAndValidateClockOut() helpers; wired into actionModalSave (~86), actionCreate (~242), actionUpdate (~322); canDeleteClocking() RBAC check + actionDelete gate; removed duplicate delete log
  - path: backend/views/agency/_form.php
    note: removed the HTML5 min= pins on clockInDate/clockOutDate inputs (lines ~213/217)
  - path: backend/views/agency/index.php
    note: "RBAC-gated delete button + Swal confirm + delete JS; actions column: nowrap, tighter padding, width 200px"
  - path: common/models/AgencyClockings.php
    note: "beforeDelete(): single readable audit entry (loggedBy = 'Deleted by <username>')"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260706-0759
    model: claude-opus-4-8
    started: 2026-07-06T07:59:08Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-06T07:59:08Z
    ended: 2026-07-06T07:59:31Z
    summary: Improved the screens managers use to edit agency workers' shifts, addressing several issues the transport manager raised. Managers can now change a shift's date freely (including onto the next day), so shifts that run past midnight can be recorded accurately instead of fudging the start time. If a manager types a finish time but forgets the date, the system now fills the date in sensibly (same day, or the next day when the finish time is before the start time) so the shift calculates real hours instead of showing zero. The system now also blocks obviously-wrong entries where the finish is before the start, with a clear message, so bad clockings can't be saved silently. Managers with the right permission can now delete a duplicate/mistaken shift via a button with a confirmation prompt, and every delete is recorded in the audit log as "Deleted by <name>" with a full copy of the shift, so anything removed can be traced. Access to that delete button is controlled through the normal permissions system (a 'Delete Agency Shift' permission plus superusers), not hardcoded names, so who can delete is managed from the admin panel. The workers' own clock-in screens are untouched — only the manager tools changed. Without this, past-midnight shifts stayed inaccurate, forgotten dates silently produced zero-hour shifts on payroll reports, wrong dates could be entered unnoticed, and duplicates couldn't be removed. Changes are written and syntax-checked; not yet committed/deployed.
    test_plan: |-
      Prereq: test locally, then commit/deploy. The 'Delete Agency Shift' RBAC permission is created + assigned already.

      Date editing / cross-midnight (full edit):
      1. Open a shift in full edit (/agency/update); confirm you can now move the date backward and to the next day (previously blocked). Quick edit already allowed this.
      2. Enter a shift clocking in 22:00 one day and out 02:00 the next day; confirm hours = ~4h (minus the 30-min break), not 0.

      Smart end-date default:
      3. On a shift, enter a clock-out TIME but leave the clock-out DATE blank, save. Confirm the date fills to the clock-in date and hours calculate (not 0).
      4. Same but with an out-time earlier than the in-time (e.g. in 23:00, out 01:00, no out-date) → confirm the out-date rolls to the next day and hours are correct.

      Out-after-in validation (all three paths):
      5. Quick edit: set clock-out before clock-in (same or earlier date) → Save → error in the modal ("Clock-out must be after clock-in"), nothing saved.
      6. Full edit: same → error shown on the form, nothing saved.
      7. Create a new clocking with out before in → error on the form, and confirm NO row was created.

      Delete + audit + permissions:
      8. As a user WITH 'Delete Agency Shift' (or a superuser): the red bin shows on the list; click → confirm prompt → shift deletes.
      9. Check agencyclockings_log: exactly ONE new row for that delete, marked "Deleted by <your name>", with the shift's details.
      10. As a user WITHOUT the permission: no bin button; a direct POST to /agency/delete returns 403.

      Layout + no regressions:
      11. Confirm the four action icons (view/edit/quick-edit/delete) sit on one row.
      12. Confirm an ordinary edit still logs to agencyclockings_log as "Updated by <name>".
      13. Employee side: confirm the worker clock-in/kiosk flow is unchanged (not affected by any of the above).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Added docs/controllers/backend/AgencyController/prepareAndValidateClockOut.md, .../actionDelete.md, docs/models/AgencyClockings/beforeDelete.md (new, need committing).
labels:
  - agency
  - clockings
  - transport
  - payroll
attention: null
version: 15
---

## Request (from Rob, Transport Manager)
Several issues managing agency shifts (agency clockings):
1. Full edit wouldn't let you move the date back, but quick edit would — and shifts couldn't span midnight, so past-midnight work got fudged into the start time.
2. Entering a finish **time** without a **date** saved 0 hours (report shows start+end but calculates nothing; the row can also drop off the report).
3. No way to remove an accidental duplicate shift.
Plus: prevent people manually logging impossible/incorrect dates; and log deletes so they can be traced.

## What was done
**1. Date editing / cross-midnight** — removed the HTML5 `min` pins on the full-edit clock-in/out date inputs (`views/agency/_form.php`). Full edit now matches quick edit (any date, incl. next-day clock-out). The minutes calc was already midnight-aware.

**2. Smart end-date default** — `resolveClockOutDate()`: when a clock-out time is entered but the date is blank, defaults the clock-out date to the clock-in date, rolling to **+1 day if the out-time is earlier than the in-time** (past midnight). Fixes the silent 0-hours + report-drop.

**3. Out-after-in validation** — `prepareAndValidateClockOut()` runs on all three manual save paths (quick edit, full edit, create) BEFORE any write; applies the default then rejects when clock-out ≤ clock-in with a clear message. Saves use `save(false)` so this explicit guard is what blocks bad data. Create validates before the first insert (no bad row created).

**4. Delete a shift** — added a red trash button (Swal confirm) to the clockings list (`views/agency/index.php`). Access via RBAC: `Delete Agency Shift` permission + SuperUsers, checked for both button visibility and server-side (`canDeleteClocking()`). No hardcoded user ids. (We initially exempted the route via allowActions, then reverted that in favour of proper RBAC — `main.php` unchanged.) The `Delete Agency Shift` permission was created and assigned in the RBAC panel.

**5. Single delete audit entry** — the delete is snapshotted to `agencyclockings_log` by `AgencyClockings::beforeDelete()`, now a single clean row marked `loggedBy = "Deleted by <username>"` (readable). Removed the duplicate `writeClockingLog` call in `actionDelete`.

**6. Action column layout** — the 4 icons (view/edit/quick-edit/delete) now fit on one row (`white-space: nowrap`, tighter padding, column widened to 200px).

## Not touched (verified)
Employee-facing clock-in/out (`actionClockin`, kiosk `actionSaveCustomerInput`) and `computeMinutesWorked` are unchanged — only the manager edit/create/delete screens changed.

## Deploy / setup notes
- RBAC `Delete Agency Shift` permission: created + assigned (done). Until assigned, only SuperUsers can delete.
- Docs added under docs/controllers/backend/AgencyController/ and docs/models/AgencyClockings/.

## Conversation

**2026-07-06 07:59 claude-code:** Run run-20260706-0759 completed — Improved the screens managers use to edit agency workers' shifts, addressing several issues the transport manager raised. Managers can now change a shift's date freely (including onto the next day), so shifts that run past midnight can be recorded accurately instead of fudging the start time. If a manager types a finish time but forgets the date, the system now fills the date in sensibly (same day, or the next day when the finish time is before the start time) so the shift calculates real hours instead of showing zero. The system now also blocks obviously-wrong entries where the finish is before the start, with a clear message, so bad clockings can't be saved silently. Managers with the right permission can now delete a duplicate/mistaken shift via a button with a confirmation prompt, and every delete is recorded in the audit log as "Deleted by <name>" with a full copy of the shift, so anything removed can be traced. Access to that delete button is controlled through the normal permissions system (a 'Delete Agency Shift' permission plus superusers), not hardcoded names, so who can delete is managed from the admin panel. The workers' own clock-in screens are untouched — only the manager tools changed. Without this, past-midnight shifts stayed inaccurate, forgotten dates silently produced zero-hour shifts on payroll reports, wrong dates could be entered unnoticed, and duplicates couldn't be removed. Changes are written and syntax-checked; not yet committed/deployed.

---

**2026-07-06 08:06 — you**

sorted out the no date save, and clocking spams next day
