---
id: T-0573
title: ZL Run Overview Updates.
type: feature
state: triaged
priority: p2
created: 2026-07-14T16:22:23Z
updated: 2026-07-14T16:51:10Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 41984
reporter: null
assignee: null
acceptance_criteria:
  - "Unload-view item issue modal replaced with driver-app-style flow: pick ONE issue type, then qty stepper, photo capture (enforced when IssueTypeOptions.photoRequired=1), fault + comments — instead of the current 3-counts-at-once form. Submits through the existing /issue-reports/save-issues-new path (Issues + IssueChosenOptions), so YaRunContracts::checkItemCount still resolves the shortfall."
  - 'Quick-missing prompt on unload view only: when complete-unload finds entered < expected, show a yes/no prompt "You entered X/Y — report N missing?". Yes creates a Missing issue (issueTypeOptionID 12, qty = shortfall, no photo) and lets the unload complete without opening the full modal; No opens the full issue form as today.'
  - "Migration sets IssueTypeOptions.photoRequired=0 for Missing (option 12) — DECIDED: you cannot photograph an item that isn't there. Driver app then treats the photo as optional for Missing automatically via its issue-types sync (isPhotoRequired reads the flag); driver-app test fixture updated to match (driver-app/src/logic/__tests__/issueTypes.test.ts:34)."
  - "Runs-overview-new unloading side: red fast-forward manager-override icon on Back-at-Base runs (status 40–69) with contracts not yet counted. Visible only to users holding the new RBAC permission; the backend endpoint re-checks the permission server-side (Yii::$app->user->can), marks all the run's contracts unloaded, creates a contract-level issue \"Unloaded Without System Count\" (new IssueTypeOptions entry) on each contract, and marks the run complete (status 70)."
  - "New RBAC permission seeded via migration (pattern: m260402_150000_add_choose_warehouse_photos_permission) and assigned to BOTH Warehouse Manager and WHSupervisors (DECIDED); users without it never see the icon and get a 403/error from the endpoint."
  - '"Unloaded Without System Count" issue type does NOT appear in any manual issue-type pick list (warehouse modal or driver app) — system-generated only (DECIDED). It must still display correctly in issue lists/resolution views (all-issues, contract-history-new).'
  - "Left-side fast-forward on runs with no deliveries sets Loaded/Ready (status 20, mark-loaded semantics) instead of jumping to dispatched (status 30). DECIDED: run remains in the Loading-Done section until the driver actually departs."
  - "Existing flows unaffected: normal unload with correct counts, driver-app issue reporting (other than Missing photo becoming optional), and the loading-side flow for runs WITH deliveries all behave as before."
out_of_scope: []
code_anchors:
  - path: backend/views/warehouse/unload-run.php
    symbol: checkCounts()
    lines: 1255-1288
    note: complete-unload count check -> /run-contracts/check-count -> getIssueReportForm on shortfall; quick-missing prompt hooks in here
  - path: common/models/YaRunContracts.php
    symbol: checkItemCount
    lines: 1716-1855
    note: shortfall resolve check reads IssueChosenOptions issueTypeOptionID=12 (Missing) at 1824
  - path: common/models/YaRunContracts.php
    symbol: getIssueReportFormNew
    lines: 2087-2149
    note: current 3-count (damaged/dirty/missing) form to be replaced with driver-app-style flow
  - path: backend/controllers/IssueReportsController.php
    symbol: actionGetIssueForm / actionSaveIssuesNew
    lines: 234-321
    note: active warehouse issue endpoints; save writes Issues + IssueChosenOptions (same tables as driver app)
  - path: backend/web/js/backend-a.js
    symbol: getIssueReportForm
    lines: 800-820
    note: fetches modal HTML from /issue-reports/get-issue-form
  - path: driver-app/src/components/ItemIssueModal.tsx
    note: "reference UX: one issue type -> qty stepper -> photo enforced per issue type -> fault -> comments"
  - path: driver-app/src/logic/issueTypes.ts
    symbol: isPhotoRequired
    lines: 52-54
    note: photo rule = IssueTypeOptions.photoRequired === 1
  - path: backend/controllers/ApiDriversController.php
    symbol: actionSyncIssue
    lines: 1731-1894
    note: driver-app submit endpoint, reference for payload shape + photo handling
  - path: backend/views/warehouse/runs-overview-new.php
    symbol: runComplete()
    lines: 432-484
    note: no-deliveries fast-forward currently posts /runs/fast-forward-loaded (status 30); change to Loaded/Ready
  - path: backend/controllers/RunsController.php
    symbol: actionMarkLoaded / actionFastForwardLoaded
    lines: 71-162
    note: mark-loaded sets status 20; fast-forward-loaded sets status 30 + pushed=1 (added May 2026 in 88057d9c as error-log fix)
  - path: common/models/RunsOverview.php
    lines: 105, 202-213, 650
    note: fast-forward icon render conditions (loading side, no activeDeliveries) and unload-side truck icon (status 40-69, no collections) where the red override icon goes
  - path: backend/controllers/WarehouseController.php
    symbol: actionMarkUnloaded
    lines: 1193-1237
    note: "existing per-item unload: item status 60 + Itemcountlog type 5, run status 60; override should reuse this logic per contract"
  - path: console/migrations/m260402_150000_add_choose_warehouse_photos_permission.php
    note: RBAC permission seed pattern (auth_item + auth_item_child)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 7
attachments:
  - key: tickets/T-0573/1784046183546-Screenshot_2026-07-14_at_17-22-41_.png
    filename: Screenshot 2026-07-14 at 17-22-41 .png
    content_type: image/png
    size: 168400
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-14T16:23:04Z
---

A few things that have been a pain point for the warehouse. Zac has said the runs overview is getting cluttered with warehouse often missing the unloading on the app (they do actually unload it but do not put the counts in properly etc) 

One of the problems is that when they enter a count less than there shuld be - the system expects an issue report for the number missing (it does a check on complete unload for any counts less than expected and then triggers the issue report modal)The problem with the issue reporting from the system is that it asks you for 3 seperate counts when reporting any item issue (damaged missing dirty) then some of the options have photo required. I want to change the issue reporting view to work as it does in the driver app. This still forces photos where required but the ui and functionality are better in the driver app. 

In addition to the new UI for issue reporting, we also need a report quick missing issue. This will be for items that we enter less than the expected qty (on unload view only). It should say you have entered 20/28 do you want to report 8 missing and have a yes no button. This will make it easier for them to  report missing issues and process the unloads without as much friction. 

in addition to that on the runs overview- when the vehicle is at status back at base - can we have a manager override (same fast forward icon but in red and only allowed by certian users (we can make the backend endpoint and use RBAC to set the permissions The manager override should Mark all contracts as unloaded, create an issue report on each contract (we can create a new issue type for this (contract level - Unloaded without system count), and mark the run as complete.\
\
We also want to make a slight change to the left hand side we have an icon \<i style="margin-left:3px;font-size:1.5em; --fa-primary-color:#333 ; --fa-secondary-color:black ;float:right; " onclick="runComplete(24777, 0)" class="fas fa-fast-forward">\</i> that shows on runs with no deliveries (hence no loading needed) the fast forward button on that side currently marks the run as on the road. We want to change this to Loaded / Ready Status.

## Conversation

**2026-07-14 16:42 claude-code:** Code review of the four asks — all feasible, with one simplification and three decisions to settle before build.

**Good news on the issue-report UI swap (ask 1):** the warehouse unload modal already writes to the SAME tables as the driver app (Issues + IssueChosenOptions, via /issue-reports/save-issues-new). So this is a pure UI replacement — port the driver-app flow (pick one issue type → qty stepper → photo → fault → comments, ItemIssueModal.tsx) into the modal HTML that YaRunContracts::getIssueReportFormNew builds, keep the same save endpoint. No data-model migration needed, and the count-shortfall check (checkItemCount, which resolves against IssueChosenOptions option 12 = Missing) keeps working untouched.

**Quick-missing (ask 2):** clean hook point exists — checkCounts() in unload-run.php gets the shortfall back from /run-contracts/check-count; instead of opening the full modal, show the yes/no swal and on Yes create the Missing issue (option 12, qty = shortfall) then re-run the check.

**Manager override (ask 3):** app has proper Yii2 RBAC with a ready pattern (Choose Warehouse Photos permission, seeded by migration, checked via Yii::$app->user->can in both view and endpoint). New permission + red icon on Back-at-Base runs (status 40–69) + endpoint that reuses actionMarkUnloaded logic per contract, files the contract-level issue, sets run status 70.

**Loaded/Ready button (ask 4):** the current jump-to-status-30 (/runs/fast-forward-loaded) was added in May 2026 as an error-log fix (commit 88057d9c), not a deliberate workflow choice — so switching to mark-loaded semantics (status 20) is safe. Note the run will then sit in the Loading-Done column until the driver actually departs (the unloading column only shows status ≥ 30).

**Decisions needed:**
1. **Ask 4 side-effect** — with status 20, a collections-only run stays on the loading side until a driver starts it. Confirm with Zac that's what he wants (rather than it appearing on the unloading side immediately).
2. **Quick-missing photo rule** — the driver app's issue-type data marks Missing as photo-required. The quick yes/no flow has nothing to photograph (item simply isn't there at unload), so it would deliberately skip the photo. Confirm that's acceptable, or flip photoRequired for Missing.
3. **Which roles** get the new override permission (Warehouse Manager? WHSupervisors? named users?), and should "Unloaded Without System Count" be hidden from the manual issue-type pick list (system-generated only)?

Also flagged: there are two runs-overview implementations — runs-overview-new.php (active, renders via RunsOverview::outputRunDesktop) and the legacy runs-overview.php. All changes target the new one; worth confirming nothing still routes warehouse staff to the old page.

**2026-07-14 16:51 claude-code:** Austin settled the three open decisions (2026-07-14):

1. **Loaded/Ready button** — confirmed: a no-deliveries run should stay in the Loading-Done section at status 20 until the driver departs. Implement by switching the runComplete call from /runs/fast-forward-loaded to mark-loaded semantics.
2. **Missing photo rule** — Missing must NOT require a photo anywhere, including the driver app ("you can't photograph an item that isn't there"). Implement as a migration flipping IssueTypeOptions.photoRequired=0 for option 12 (Missing); the driver app picks that up automatically via its /api/drivers/issue-types sync. Update the driver-app test fixture (issueTypes.test.ts:34) which currently asserts photoRequired=1 for Missing.
3. **Override permission** — assign to BOTH Warehouse Manager and WHSupervisors. The new "Unloaded Without System Count" issue type is system-generated only and must be excluded from all manual issue-type dropdowns (warehouse modal and driver app), while still rendering correctly in issue lists and resolution views.

Acceptance criteria updated to reflect all three. Ticket now meets Definition of Ready (ACs + code anchors in place).
