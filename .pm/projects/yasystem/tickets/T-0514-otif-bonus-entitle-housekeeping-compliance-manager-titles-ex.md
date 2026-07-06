---
id: T-0514
title: "OTIF bonus: entitle Housekeeping + Compliance Manager titles; exclude Aaron Jarvis"
type: chore
state: review
created: 2026-07-06T04:05:52Z
updated: 2026-07-06T04:27:14Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Housekeeping (title 26) and Housekeeping Team Lead (title 25) are entitled to OTIF under the In-Full (warehouse) metric."
  - "[x] Compliance Manager (title 24) is entitled to OTIF under the On-Time (transport) metric."
  - "[x] On the next OTIF calculation, Macarena Lopez (871), Mariya Todorova (646) and Luigi Esposito (196) receive an OTIF bonus via their titles."
  - "[x] Aaron Jarvis (user 87) is excluded from both the OTIF bonus calc and the one-off bonus calc, and shows N/A in the payroll bonus display."
  - "[x] Other holders of the Warehouse Controller title (14) remain entitled — only Aaron (87) is excluded."
  - "[x] Both changed files pass php -l."
out_of_scope: []
code_anchors:
  - path: common/models/User.php
    note: operationalStaffIDsOtif() ~line 1536-1539 — entitlement arrays; added 24/25/26 to $departments, 25/26 to $warehouse (In-Full), 24 to $transport (On-Time)
  - path: backend/controllers/AccountsController.php
    note: OTIF_EXCLUDED_USER_IDS const ~line 92 — added 87 (Aaron Jarvis). Applied at 5528/5864 (display) and 6640-6641 / 6754-6755 (OTIF + one-off bonus calc array_diff)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260706-0406
    model: claude-opus-4-8
    started: 2026-07-06T04:06:01Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-06T04:06:01Z
    ended: 2026-07-06T04:06:23Z
    summary: "Updated who qualifies for the OTIF performance bonus. Three staff on newly-created job titles now qualify: Housekeeping and Housekeeping Team Lead are tied to the In-Full quality measure, and Compliance Manager is tied to the On-Time delivery measure. Separately, one Warehouse Controller (Aaron Jarvis) is now excluded from receiving the bonus even though his job title normally qualifies. Entitlement to this bonus isn't a setting anyone can change in a screen — it's written into the code as fixed lists of job titles (for who's in) and staff ids (for who's specifically excluded), so a manager can't self-serve it; a developer has to edit those lists. Without this change, the new titles would have been silently left out of every future bonus run and the manager would keep having to calculate them by hand, and the excluded person would have kept receiving a bonus he shouldn't. Note: because inclusion is by job title, this covers everyone holding those titles, not just the three named; and the exclusion list covers both the OTIF bonus and the separate one-off bonus, so the excluded person is out of both. Changes are written and syntax-checked but not yet committed or deployed, and the current quarter was already handled manually — so this takes effect from the next bonus calculation once deployed."
    test_plan: |-
      Prereq: deploy the working-tree changes (they're uncommitted on master today). Verify on staff payroll.

      Entitlement (new titles):
      1. Run "Calculate OTIF Bonuses" for a period where the quarter hit at least Target 1.
      2. Confirm Macarena Lopez (871, Housekeeping) and Mariya Todorova (646, Housekeeping Team Lead) get an OTIF bonus driven by the In-Full % target (warehouse bucket).
      3. Confirm Luigi Esposito (196, Compliance Manager) gets an OTIF bonus driven by the On-Time % target (transport bucket).
      4. Confirm other staff sharing those three titles also now appear (entitlement is title-based, not per-person) — sanity-check the count didn't balloon unexpectedly.

      Exclusion (Aaron Jarvis, 87):
      5. In the same OTIF calculation, confirm Aaron Jarvis (87) gets NO OTIF bonus row created (he's array_diff'd out before hours are pulled).
      6. Run "Calculate One-off Bonuses" and confirm Aaron (87) is likewise excluded there (same list drives both).
      7. In the staff-payroll bonus display, confirm Aaron's bonus cell shows N/A rather than a figure.
      8. Confirm OTHER Warehouse Controllers (title 14) still receive OTIF — only Aaron (87) is removed, not the whole title.

      Regression:
      9. Confirm the previously-excluded user (787, Orfield) is still excluded — the list is now [787, 87].
      10. Confirm existing entitled titles (drivers on On-Time, warehouse on In-Full) are unaffected — the new ids were appended, none removed.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - otif
  - payroll
  - bonus
attention: null
version: 12
---

## Request
A manager emailed: due to newly-created job titles, OTIF bonus entitlement needs updating (they'd manually processed the current quarter):

**Entitled to OTIF:** Macarena (Housekeeping), Mariya (Housekeeping), Luigi (Compliance).
**Excluded from OTIF/bonus:** Aaron Jarvis (Warehouse Controller).

They also asked: "how am I excluding people from OTIF — is it a field in a table, or hardcoded?"

## How OTIF entitlement works (answer: hardcoded, no DB field / no UI)
- **Inclusion is by job title** — hardcoded arrays of `department_positions.id` in `common/models/User.php` `operationalStaffIDsOtif()`. An employee is entitled if their `user.department` (their title id) is in the list AND `allowLogin = 1`. `warehouse` ids get the **In-Full** multiplier; `transport` ids get the **On-Time** multiplier. New titles are NOT auto-included.
- **Exclusion is by individual user id** — a hardcoded `OTIF_EXCLUDED_USER_IDS` const in `backend/controllers/AccountsController.php`. It overrides inclusion and is applied in both the OTIF bonus calc AND the one-off bonus calc (plus payroll display).
- `OtifTargets` / `staff_payroll_otif` tables are quarterly targets + calculation *output*, not entitlement config.

## Resolved user ids / titles (from live DB lookup)
- Macarena Lopez (871) — Housekeeping (title 26)
- Mariya Todorova (646) — Housekeeping Team Lead (title 25)
- Luigi Esposito (196) — Compliance Manager (title 24)
- Aaron Jarvis (87) — Warehouse Controller (title 14, already an entitled title → hence exclude by user id)

## Changes made
- `User.php`: added titles **25, 26 → warehouse (In-Full)** and **24 → transport (On-Time)**, plus all three to the master `$departments` filter. Bucket split confirmed with Zsolt (Housekeeping = In-Full; Compliance = On-Time).
- `AccountsController.php`: added **87** to `OTIF_EXCLUDED_USER_IDS` → `[787, 87]`.

## Notes
- Entitlement is **title-based**, so this entitles *everyone* with those titles, not only the three named.
- The exclusion list drives **both** OTIF and one-off bonuses, so Aaron is out of both (confirmed intended).
- Applies from the **next** OTIF calculation; the current quarter was processed manually by the manager.
- Working-tree changes only, on protected `master` — not yet committed/deployed.

## Conversation

**2026-07-06 04:06 claude-code:** Run run-20260706-0406 completed — Updated who qualifies for the OTIF performance bonus. Three staff on newly-created job titles now qualify: Housekeeping and Housekeeping Team Lead are tied to the In-Full quality measure, and Compliance Manager is tied to the On-Time delivery measure. Separately, one Warehouse Controller (Aaron Jarvis) is now excluded from receiving the bonus even though his job title normally qualifies. Entitlement to this bonus isn't a setting anyone can change in a screen — it's written into the code as fixed lists of job titles (for who's in) and staff ids (for who's specifically excluded), so a manager can't self-serve it; a developer has to edit those lists. Without this change, the new titles would have been silently left out of every future bonus run and the manager would keep having to calculate them by hand, and the excluded person would have kept receiving a bonus he shouldn't. Note: because inclusion is by job title, this covers everyone holding those titles, not just the three named; and the exclusion list covers both the OTIF bonus and the separate one-off bonus, so the excluded person is out of both. Changes are written and syntax-checked but not yet committed or deployed, and the current quarter was already handled manually — so this takes effect from the next bonus calculation once deployed.

**2026-07-06 04:27 claude-code:** **Docs added (2026-07-06):** documented the OTIF entitlement/exclusion logic (previously undocumented) in the repo's per-method docs convention:
- `docs/models/User/operationalStaffIDsOtif.md` — how entitlement works (hardcoded job-title arrays; warehouse=In-Full, transport=On-Time; how to add a title), with the 2026-07 title additions.
- `docs/controllers/backend/AccountsController/actionCalculateOtifBonuses.md` — the `OTIF_EXCLUDED_USER_IDS` exclusion list (how to exclude a person; note it drives BOTH OTIF and one-off bonuses + the N/A display), plus the calc flow.

The two docs cross-link. Working-tree only, uncommitted alongside the code change.
