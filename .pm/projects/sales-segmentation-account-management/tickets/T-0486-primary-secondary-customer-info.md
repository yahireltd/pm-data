---
id: T-0486
title: Primary / Secondary Customer Info
type: feature
state: triaged
priority: p2
created: 2026-06-30T12:43:54Z
updated: 2026-06-30T16:11:37Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-012
children: []
order: 8192
reporter: null
assignee: null
acceptance_criteria:
  - Model a PRIMARY contact (decision-maker / 'home' record) vs SECONDARY contacts per economic account, so ownership, level and rolled-up value attach to ONE record.
  - Reconcile with mergedIntoCustomerID + the indexed email_domain (T-0480) and the home_customerID rule (merge-primary, else highest-lifetime same-domain row) from the account-level design.
  - Customer / my-accounts screens show which contact is primary; satellite contacts point to the home row and don't double-count.
  - This is what makes the lane's customerID identity fix (already shipped) durable across the base.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: home-row model (2.1) / home_customerID rule
  - path: common/models/YaCustomers.php
    role: mergedIntoCustomerID, getSameDomainAccounts, email_domain
relates:
  - T-0480
  - T-0457
  - T-0495
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 2
---

## Problem
One organisation is split across many contact rows (and typo'd/`.comOLD` emails). Ownership + value rollups need a
single primary record per economic account.

## Design notes
Define primary (home) vs secondary contacts; primary = merge-primary else highest-lifetime same-domain row
(home_customerID). Satellites point to it (no N-fold inflation). Built on T-0480's email_domain + mergedIntoCustomerID.
This generalises the customerID identity resolution we already shipped on the fast-track lane.

## Relates
T-0480, T-0457, T-0495.