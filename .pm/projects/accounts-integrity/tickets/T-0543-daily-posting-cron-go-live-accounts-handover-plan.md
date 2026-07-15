---
id: T-0543
title: Daily posting cron go-live & accounts handover plan
type: chore
state: review
created: 2026-07-10T21:28:58Z
updated: 2026-07-15T16:05:10Z
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
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Canary run on live: clean digest, zero orphans/duplicates, run-issues reviewed"
  - "[x] Parallel week completed: cron posted everything first; accounts' manual runs were no-ops; no mutex conflicts"
  - "[x] Accounts briefed and formally handed over: manual posting stopped, digest/triage routine owned by accounts with an agreed escalation rule"
  - Posting-window offsets confirmed with accounts (or adjusted)
  - Rollback path documented and tested in principle (cron disable + manual post available)
  - Sandbox refresh cron re-enabled on live
out_of_scope: []
code_anchors:
  - path: console/controllers/XeroPostController.php
    symbol: actionDaily
    note: the cron entry point (post=7, sweep=8 day offsets)
  - path: console/migrations/m260709_160000_create_xero_oauth_tokens_table.php
    note: the one migration this release runs
  - path: common/services/XeroApiService.php
    note: DB token store + locked refresh primed by /xero/login
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260713-1307
    model: claude-fable-5
    started: 2026-07-13T13:07:24Z
    status: in_progress
    progress:
      - at: 2026-07-13T13:11:50Z
        note: |-
          Stage 0 code work done. Pre-flight found + fixed a real release risk: the /xero/login callback only guarded tenant selection on the SANDBOX side — on live it took getConnections()[0], and the same Xero user is connected to BOTH the live org and the Demo Company through this app, so live's token store could silently aim all posting at demo. Live now mirrors the sandbox guard in reverse (refuses demo-only), and the callback prints WHICH organisation was connected by name (the runbook's verify step). Commit 425c6c2a on t0534.

          Master fast-forwarded 74a39606 → 425c6c2a (clean, zero conflicts — the branch already contained Austin's Jul-9 heal-page commits) and pushed. Exactly ONE migration ships (m260709_160000 xero_oauth_tokens). t0538 merged the guard (37de5d41) and the test box is updated, so the branches stay a coherent superset.

          Extracted from the handover for the runbook: the 3 linked-email data fixes (5341 double-@, 13658 trailing dot, 59454 phone-as-email), the census tripwire query, the exact cron lines. Next: Austin executes the live steps (pull, migrate, login+verify tenant, SQL fixes, supervised canary), then cron + parallel week per the staged plan.
      - at: 2026-07-13T13:30:49Z
        note: "Backlog-fill feature added and rehearsed. xero-post/daily now takes --date / --from/--to (validated, 40-day cap, circuit breaker between windows; cron default unchanged) — commit 162c4688, on master + t0538 + test box. Demo Company was found reset (stale token 403s on all 12 posting steps — which incidentally proved graceful total-API-failure behaviour: clean completion, verbatim logging, issue digest). Full ritual re-run: Austin reset+login+bank accounts; API-created revenue 201/202; shortcode param → !!6J3Q; prep-sandbox blanked dead-org GUIDs. REHEARSAL: real posting run via --date=2026-06-20 against the fresh org — 21/21 window docs posted, 13 customers, 8 payments, 10 deposit allocations, zero duplicates (census empty), zero stranded attempts, all-clear digest delivered. Live runbook unchanged and ready: master at 162c4688; Austin's canary becomes --from/--to over the paused dates."
labels:
  - xero
  - release
  - accounts-handover
attention: null
version: 10
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
