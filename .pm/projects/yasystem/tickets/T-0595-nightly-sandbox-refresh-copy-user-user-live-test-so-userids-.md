---
id: T-0595
title: "Nightly sandbox refresh: copy user.user live → test so userIDs stay in sync"
type: chore
state: done
created: 2026-07-15T17:59:59Z
updated: 2026-07-16T13:09:41Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] After the nightly refresh, user.user on test matches live (same COUNT(*) and MAX(userID))"
  - "[x] Rest of the test-side `user` db (auth_*, django_*, user_types) is NOT overwritten by the refresh"
  - "[x] Refresh-plan confirmation output lists the extra tables being copied"
  - "[x] A failed table copy fails the run with a non-zero exit (no silent partial copy)"
out_of_scope: []
code_anchors:
  - path: console/controllers/SandboxController.php
    note: EXTRA_TABLES const + copyTable(); copies run in actionRefresh after the per-database refresh loop
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1800
    model: claude-fable-5
    started: 2026-07-15T18:00:29Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-15T18:00:29Z
    ended: 2026-07-15T18:00:54Z
    summary: Every night the test system is refreshed with a copy of the live data, but the list of user accounts lives in a separate database that the refresh never touched. Whenever a new user was added on live, the test system ended up with records pointing at users it didn't know about, causing intermittent errors on test. We extended the nightly refresh so it also copies the user list from live to test, keeping the two in step automatically. If we did nothing, every new starter added on live would keep producing these mismatches on test until someone copied the users across by hand. Only the user list is copied — the test system's own logins/sessions in that database are left alone. Austin is committing and deploying the change himself.
    test_plan: |-
      1. Deploy: confirm the deploy reached the box whose crontab actually runs `./yii sandbox/refresh` (believed to be the live server). If the cron runs from the test/sandbox box instead, that checkout needs the update too or nothing changes.
      2. Run `./yii sandbox/refresh --yes` manually once (or wait for tonight's cron). Check the printed refresh plan now includes "Extra tables to copy: user.user", and the output ends with a successful "Copying table 'user.user' live → test" section followed by "All done".
      3. Verify sync: run `SELECT COUNT(*), MAX(userID) FROM user.user;` on BOTH the live host and the test host — the numbers must match.
      4. Permissions edge case (the one untested assumption): if the copy step fails with an access-denied error, the RDS user needs grants on the `user` schema — the existing job only ever touched `yahire_db`. A failure exits non-zero, so a broken nightly run would be visible, not silent.
      5. Confirm the rest of the test-side `user` db was NOT overwritten: spot-check that `auth_user` / `django_session` on test still hold their pre-run test values.
      6. Cross-impact: confirm the preserved tables still survive the refresh as before — especially `yahire_db.xero_oauth_tokens` on test must still hold the TEST Xero token, not live's (T-0534 safety), plus `sketch_plans` and `customer_sales_scores`.
      7. Tell testers: any user accounts created directly on the test box will now be wiped by each nightly run (same as yahire_db data).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 10
---

## Problem

The nightly sandbox refresh (`./yii sandbox/refresh`, run on the sandbox controller) copies live over test for `yahire_db` only. The separate `user` database is never touched, so users created on live have userIDs that don't exist on test. After each nightly copy, test-side rows referencing those userIDs point at missing users and cause intermittent test-box issues.

## Fix

Added an `EXTRA_TABLES` step to the sandbox refresh: after the database refreshes complete, each listed table is streamed live → test with the same pipefail-guarded mysqldump pipe. Starts with just `user.user` — the rest of the `user` db (django auth tables, sessions, `user_types`) is deliberately left alone on test. The refresh-plan summary now lists the extra tables so the confirmation output reflects what will be copied.

Notes:
- The copy replaces the test table wholesale (DROP/CREATE + FK checks disabled during import), so any user rows created directly on test disappear each night — same behaviour as `yahire_db`.
- Untested assumption at build time: the RDS credentials have privileges on the `user` schema on both hosts (the existing job only ever touched `yahire_db`). First run verifies this.
- Austin is committing and deploying to live himself (2026-07-15).

## Conversation

**2026-07-15 18:00 claude-code:** Run run-20260715-1800 completed — Every night the test system is refreshed with a copy of the live data, but the list of user accounts lives in a separate database that the refresh never touched. Whenever a new user was added on live, the test system ended up with records pointing at users it didn't know about, causing intermittent errors on test. We extended the nightly refresh so it also copies the user list from live to test, keeping the two in step automatically. If we did nothing, every new starter added on live would keep producing these mismatches on test until someone copied the users across by hand. Only the user list is copied — the test system's own logins/sessions in that database are left alone. Austin is committing and deploying the change himself.

---

**2026-07-16 13:09 — you**

Done and ran last night ok
