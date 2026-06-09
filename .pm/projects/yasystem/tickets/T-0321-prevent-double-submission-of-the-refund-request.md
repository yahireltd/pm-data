---
id: T-0321
title: Prevent double-submission of the refund request
type: bug
state: triaged
created: 2026-06-09T19:15:03Z
updated: 2026-06-09T19:27:38Z
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
version: 2
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The "Process Refunds" buttons can be clicked twice (or two staff members can click at the same time) and nothing stops both requests from running. Because refunds send real money through Stripe, a double-click could refund a customer twice.

## Context

Found during the C090586 refund investigation (it was not the cause there, but it is an open hole). The modal buttons fire an AJAX request with no disable-on-click, and the server side takes no lock on the contract, so two racing requests would both read the same balances and both refund.

Fix both layers: disable the buttons once clicked (re-enable on failure), and add a server-side lock per contract (e.g. row lock / mutex) so a second concurrent refund request for the same contract is rejected with a clear message instead of running.