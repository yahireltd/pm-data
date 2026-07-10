---
id: T-0543
title: Daily posting cron go-live & accounts handover plan
type: chore
state: triaged
created: 2026-07-10T21:28:58Z
updated: 2026-07-10T21:28:58Z
project: accounts-integrity
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee: null
acceptance_criteria:
  - "Canary run on live: clean digest, zero orphans/duplicates, run-issues reviewed"
  - "Parallel week completed: cron posted everything first; accounts' manual runs were no-ops; no mutex conflicts"
  - "Accounts briefed and formally handed over: manual posting stopped, digest/triage routine owned by accounts with an agreed escalation rule"
  - Posting-window offsets confirmed with accounts (or adjusted)
  - Rollback path documented and tested in principle (cron disable + manual post available)
  - Sandbox refresh cron re-enabled on live
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - xero
  - release
  - accounts-handover
attention: null
version: 1
---

## Problem

Going live with T-0534 includes switching Xero posting from accounts' manual weekly batches to a **daily 06:00 cron — for the first time ever**. This both relieves accounts of the posting task and removes the failure mode that started this programme (the post-all double-click). It is an operational/workflow change for the accounts team, not just a crontab line, and needs its own staged go-live.

## Staged plan

**Stage 0 — prerequisites** (part of the T-0534 release):
- Branch merged + deployed; `xero_oauth_tokens` migration run; `/xero/login` completed on live (DB token store primed); linked-email data fixes applied.

**Stage 1 — canary (supervised):**
- One manual run of `php yii xero-post/daily` on live, off-peak, Austin watching.
- Verify: digest email received; `/xero/run-issues` reviewed; zero orphans/duplicates (completeness-style spot check); posting log clean.

**Stage 2 — parallel week:**
- Enable cron `0 6 * * *`. Accounts KEEP their manual routine for one week.
- Expected evidence: their post-all finds nothing left to post (the cron got there first). Their clicks are safe no-ops under the `yh_xero_post_all` mutex — deliberately, this is the week that proves the mutex kills the double-click failure mode.

**Stage 3 — handover:**
- Accounts formally stop manual posting.
- Brief accounts (15 min): the daily digest email (all-clear vs issues), the `/xero/run-issues` triage page (every row deep-links the Xero doc + the contract's manual-refunds page), what 'Rounding adjustment' / 'Balance adjustment' lines mean, and when to escalate to IT.
- Agree the escalation rule: digest issue not understood within a day → Austin.

**Stage 4 — rollback path (documented, hopefully unused):**
- Disable the cron; accounts resume manual posting via `/xero/post`, which carries all the same safeguards.

## Open decision (needs accounts input)
The daily command posts documents at **T-7/T-8 day windows** by default. Confirm with accounts that a rolling 7-day lag matches their expectations (their old batching was weekly anyway, but the lag is now systematic rather than chosen). Adjust the offsets if they want documents in Xero sooner.

## Also
- Re-enable the sandbox-refresh cron on live after the T-0534 merge (it was paused to freeze the test snapshot).
- After a clean parallel week, record the outcome on MS-002 and the project.