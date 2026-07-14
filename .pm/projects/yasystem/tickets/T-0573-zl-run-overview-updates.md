---
id: T-0573
title: ZL Run Overview Updates.
type: feature
state: review
priority: p2
created: 2026-07-14T16:22:23Z
updated: 2026-07-14T18:07:57Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 41984
reporter: null
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260714-1654
    model: claude-fable-5
    started: 2026-07-14T16:54:08Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-14T16:54:08Z
    ended: 2026-07-14T17:19:22Z
    summary: "Built all four warehouse pain-point fixes Zac asked for. (1) Reporting an item issue at unload no longer walks staff through every issue type one after another — they now pick what's wrong (Missing / Damaged / Dirty / Not Ours) and only answer that one question, matching how the driver app works. Photos are still forced for damaged and dirty items. (2) When staff enter fewer items than expected and press complete, they now get a simple \"you entered 20/28 — report 8 missing?\" yes/no prompt; Yes files the missing report and finishes the unload in one tap instead of opening the full issue form. (3) Managers (Warehouse Manager and WHSupervisors roles only) get a red fast-forward button on Back-at-Base runs that were unloaded without counts being entered: it marks every outstanding contract as unloaded, files an \"Unloaded Without System Count\" issue on each so goods-in knows to double-check, and completes the run — clearing the clutter from the runs overview. (4) The fast-forward button on runs with no deliveries now marks the run Loaded/Ready (staying in the Loading Done column until the driver departs) instead of wrongly showing it as on the road. Also: \"Missing\" no longer demands a photo anywhere, including the driver app — you can't photograph an item that isn't there. Without these changes warehouse staff would keep skipping the clunky flows, leaving runs stuck on the overview and counts missing from the system."
    test_plan: |-
      **Pre-req: run the migration** (test box first): `php yii migrate` — applies m260714_170000_t0573_warehouse_unload_overrides. Verify in DB: `issue_type_options` id 12 (Missing) has photoRequired=0; new row "Unloaded Without System Count" (issueTypeID 1, showOnReports 0); `auth_item` has "Warehouse Run Override" with `auth_item_child` grants to Warehouse Manager, WHSupervisors, SuperUsers.

      **Also pre-req:** backend-a.js changed and assetManager appendTimestamp=false — hard-refresh warehouse tablets/browsers or they will run the cached old JS.

      **1. Item issue picker (unload view)**
      - Open /warehouse/unload-run?id=<run> for a Back-at-Base run, press "Report Issue" on a contract, then "Report Issue" on an item.
      - Expect: a radio prompt "What is wrong with the item?" (Missing / Damaged / Dirty / Not Ours) — NOT four sequential count questions.
      - Pick Damaged, enter a count > 0 → photo capture is forced. Pick Missing → no photo demanded. Cancel on the radio → nothing recorded.
      - Label under the item updates with the count; "Save Issue Report" still requires the comments box and saves successfully (check contract history shows the issue).
      - Cross-impact: the same modal is used from contract-history-new.php and collection-master-sheet.php "Report Issue" buttons — open one of each and confirm the item flow works there too.

      **2. Quick-missing prompt (unload view only)**
      - On an unload, enter LESS than expected for an item (e.g. 20 of 28), tick items, press Complete Contract.
      - Expect: "Report Missing Items?" prompt showing "you entered 20/28 — report 8 missing?".
      - Yes → unload completes with no further forms; contract history shows a Missing issue qty 8 reported at unload stage.
      - No → the full issue form opens (old behaviour) with the old error toast.
      - Edge: an item that already has a Missing issue with a DIFFERENT qty gets the full form, not the quick prompt.
      - Regression: entering correct counts completes with no prompt at all.

      **3. Manager override (runs-overview-new)**
      - As a user WITH Warehouse Run Override (or SuperUsers): on the Unloading side, a Back-at-Base run (status 40-69) with uncounted collections shows a RED fast-forward icon. Click → strong warning → confirm.
      - Expect: page reloads, run is complete (status 70, appears in Show Done); every previously-uncounted collection contract has an "Unloaded Without System Count" issue (check contract history / all-issues); item statuses set to unloaded; a catering collection lands on the cleaning list.
      - As a user WITHOUT the permission: no red icon; `curl -X POST /runs/manager-override-complete -d runID=<id>` (logged in as them) returns 403.
      - Edge: run where all contracts are already counted → no red icon (existing truck icon behaviour for zero-collection runs unchanged).

      **4. Loaded/Ready fast-forward (no-delivery runs)**
      - Loading side: a pushed run with NO deliveries shows the black fast-forward icon. Click it.
      - Expect: run gets status 20 (Loaded), stays on the LOADING side as a done row — it must NOT jump to the Unloading column (previous behaviour).
      - The driver starting the run in the driver app still moves it to on-the-road/unloading normally.
      - Edge: out-of-hours run (start before 6am/after 10pm) still shows the "Ensure keys are ready" warning first.
      - Check the mobile view (runs-overview-mobile-new) too — same button, same change.

      **5. Driver app**
      - After the migration, force an issue-types sync (or reinstall/sync screen). Reporting a Missing item issue now shows "Photo (Optional)" and submits without a photo.
      - "Unloaded Without System Count" must NOT appear in the driver app's issue-type buttons (showOnReports=0) nor in the warehouse web modal's manual list.
      - Jest: `cd driver-app && npx jest src/logic/__tests__/issueTypes.test.ts` — 14/14 pass (ran locally).

      **Not committed** — all changes are in the working tree on master per the project agent policy; php -l clean on all touched PHP, node --check clean on backend-a.js.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
    artifacts:
      - type: migration
        path: console/migrations/m260714_170000_t0573_warehouse_unload_overrides.php
        note: Missing photo flag, new issue type option, RBAC permission
      - type: endpoint
        path: backend/controllers/RunsController.php
        note: actionManagerOverrideComplete (RBAC-gated, transactional)
      - type: js
        path: backend/web/js/backend-a.js
        note: driver-app-style issue type picker in reportNewIssue; number inputs
      - type: view
        path: backend/views/warehouse/unload-run.php
        note: quick-missing yes/no prompt
      - type: model
        path: common/models/RunsOverview.php
        note: red override icon, permission + uncounted-collections gated
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-14T17:19:22Z
version: 13
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

**2026-07-14 17:19 claude-code:** Run run-20260714-1654 completed — Built all four warehouse pain-point fixes Zac asked for. (1) Reporting an item issue at unload no longer walks staff through every issue type one after another — they now pick what's wrong (Missing / Damaged / Dirty / Not Ours) and only answer that one question, matching how the driver app works. Photos are still forced for damaged and dirty items. (2) When staff enter fewer items than expected and press complete, they now get a simple "you entered 20/28 — report 8 missing?" yes/no prompt; Yes files the missing report and finishes the unload in one tap instead of opening the full issue form. (3) Managers (Warehouse Manager and WHSupervisors roles only) get a red fast-forward button on Back-at-Base runs that were unloaded without counts being entered: it marks every outstanding contract as unloaded, files an "Unloaded Without System Count" issue on each so goods-in knows to double-check, and completes the run — clearing the clutter from the runs overview. (4) The fast-forward button on runs with no deliveries now marks the run Loaded/Ready (staying in the Loading Done column until the driver departs) instead of wrongly showing it as on the road. Also: "Missing" no longer demands a photo anywhere, including the driver app — you can't photograph an item that isn't there. Without these changes warehouse staff would keep skipping the clunky flows, leaving runs stuck on the overview and counts missing from the system.

**2026-07-14 17:42 claude-code:** Local testing round 1 (automated) — all green, on Austin's local setup (austin.yahire.com serving the working tree, DB = sandbox RDS clone via Secrets Manager):

- Migration applied to the sandbox DB (only pending migration; clean apply, all three role grants inserted).
- New E2E harness `php yii t0573-e2e/run` (console/controllers/T0573E2eController.php): **25/25 assertions pass** — migration state (9), full quick-missing cycle: short unload count recorded → checkItemCount flags it → quick-missing issue saved with the exact UI payload → re-check resolves (8), manager override end-state: 2 outstanding collection contracts processed, override issue on each, items unloaded, run status 70 (8). Harness is sandbox/local-guarded, transactional, rolls back by default (`--keep` to persist for UI inspection).
- Driver-app jest suite: 42/42.
- To make the override console-testable, its core moved from the controller action into YaRuns::managerOverrideUnloadComplete() — endpoint behaviour unchanged.

Two environment notes: (1) IssueChosenOptionsResponses validates `answer` as a string — fine over HTTP (form-encoding), the harness now mirrors that wire format. (2) YaRunContracts::afterSave touches the web Response object, so the harness mounts one (known console quirk, same as T-0538's archiveNow).

Manual UI walk-through on austin.yahire.com is next (Austin).

**2026-07-14 17:57 claude-code:** Round 2 from Austin's manual testing (2026-07-14): picker functionality approved; manager override tested working. Two refinements built:

1. **Full driver-app visual for the item issue dialog** — yes, it's possible in the Yii site. The item flow is now one dialog mirroring the driver app's ItemIssueModal: red header with item name + expected qty, issue-type button grid (tap to select), quantity stepper with +/− buttons capped at what's still unreported for that item, photo section whose label switches Required/Optional per issue type (enforced on submit), a Yahire's/Customer fault toggle, and a single Submit button. The fault choice is now saved on the issue (IssueChosenOptions.fault, same field the driver app writes) — previously the warehouse web flow never captured fault.

2. **Quick-missing moved to count time** — the old "Unexpected Quantity?" alert (shown the moment a short count is typed) is now the yes/no prompt itself: "You entered 20/28 — report 8 missing?" Yes files the Missing issue immediately and turns the count green; No leaves the red border for correcting. The complete-time prompt remains as a safety net for anyone who chose No and forgot to fix the count. Over-counts (entered more than expected) keep an alert-style message. mark-counted response now includes contractID/runContractID/expectedQty (additive).

Known behaviour to be aware of (pre-existing, unchanged): if someone reports missing and then edits the count again to a different shortfall, the old issue stays and the full form is required at complete time (exact-qty match rule).

Verified: all PHP lint clean, backend-a.js parses, E2E harness re-run 25/25 (confirms the IssueFormNew fault change didn't break the save path). Awaiting Austin's re-test of the new dialog + count-time prompt (hard refresh needed — backend-a.js changed).

**2026-07-14 18:07 claude-code:** Bug found by Austin during manual testing (pre-existing, not introduced by this ticket): the truck icon on zero-collection Back-at-Base runs POSTs /runs/complete-run successfully (run completes server-side, status 70) but then throws `TypeError: can't access property "parentNode", element is null` in removeElement — the new runs-overview rows carry only data-runid, not the old view's id="R<runID>" convention, so the row never disappears until a manual refresh. Fixed in both runs-overview-new.php and runs-overview-mobile-new.php: completeRun now reloads the page on success (consistent with runComplete and managerOverrideComplete) and shows the server error on failure instead of failing silently. This bug predates T-0573 — the runs were completing fine, the UI just crashed afterwards.
