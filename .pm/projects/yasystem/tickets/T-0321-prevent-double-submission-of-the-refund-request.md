---
id: T-0321
title: Prevent double-submission of the refund request
type: bug
state: triaged
created: 2026-06-09T19:15:03Z
updated: 2026-06-09T19:53:33Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - Clicking a refund button disables all refund buttons in the modal until the request completes; they re-enable if the request fails
  - Two simultaneous refund requests for the same contract result in exactly one being processed; the other receives a clear 'refund already in progress' error
  - The lock is released properly after success, failure, and exception, so the next legitimate refund is not blocked
  - Verified with a test that fires two overlapping requests at the endpoint
out_of_scope:
  - The contract-level cash-position guard (separate ticket T-0320)
  - The deposit refund flow
code_anchors:
  - path: backend/views/contracts/manual-refund.php
    symbol: processContractRefunds
    lines: 1047-1101
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds
    lines: 1790-1850
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - incident-c090586
attention: null
version: 3
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The "Process Refunds" buttons can be clicked twice (or two staff members can click at the same time) and nothing stops both requests from running. Because refunds send real money through Stripe, a double-click could refund a customer twice.

## Build order

Depends on **T-0331 (dry-run mode)** for safe integration testing. Can be built in parallel with T-0320; touches the same controller action, so coordinate merges.

## Context

The modal buttons fire an AJAX request with no disable-on-click, and the server side takes no lock on the contract, so two racing requests would both read the same balances and both refund. Fix both layers:

1. Client: disable all refund buttons in the modal on click; re-enable on failure response.
2. Server: a per-contract lock (suggested: MySQL GET_LOCK('refund-contract-<id>') or SELECT ... FOR UPDATE on the contract row) taken at the top of the refund action; a second concurrent request fails fast with a clear 'refund already in progress for this contract' message. The lock must be released on success, failure, AND exception — in PHP, a finally block or the connection-scoped nature of GET_LOCK covers the exception path.

## Testing plan

**All on the test box (sandbox + dry-run):**

1. **UI behaviour:** open the refund modal on a contract with a refundable balance; click a refund button; verify all three action buttons grey out immediately; on a forced server error (temporarily throw), verify they re-enable and the error shows.
2. **True concurrency test:** fire two simultaneous POSTs at the refund endpoint for the same contract (two curl processes started together, or a tiny script). Run it 10+ times. Expected every time: exactly one request processes (one set of refund rows / dry-run log entries); the other returns the in-progress error and writes nothing. The dry-run request log is the assertion artifact — count rows before and after.
3. **Different contracts not blocked:** two simultaneous refunds on two DIFFERENT contracts both succeed — the lock must be per-contract, not global.
4. **Lock release on exception:** force an exception mid-refund (fixture with a missing payment row), then immediately run a normal refund on the same contract. It must not be locked out.
5. **Lock release on success:** run two refunds back-to-back (sequential) on the same contract; the second proceeds normally.
6. **Cross-impact:** the deposit-refund endpoint and the manual refund form are separate paths — verify both still work; decide explicitly (and record here) whether the same lock should also cover Process Deposit Refunds, since both flows can touch the same payments.