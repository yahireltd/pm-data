---
id: MS-002
slug: posting-pipeline-hardened-on-live-t-0534
title: Posting pipeline hardened on live (T-0534)
project: accounts-integrity
state: in_progress
order: 2048
created: 2026-07-10T21:16:32Z
updated: 2026-07-10T21:29:19Z
acceptance_criteria:
  - Branch merged to master and deployed; migration run; /xero/login on live
  - Run-post mutex, self-heal, backstops, DB token store active in production
  - Daily cron 06:00 running with digest received; canary posting run clean
  - Linked-email data fixes applied
  - Sandbox refresh cron re-enabled
  - "Accounts formally handed over from manual posting (T-0543): clean parallel week where the cron posted everything first, accounts briefed on digest + run-issues triage, escalation rule agreed, posting-window offsets confirmed with accounts"
slip_records: []
target_date: 2026-07-24
version: 4
phase_id: PH-002
---

# Posting pipeline hardened on live (T-0534)

## Acceptance criteria

## Notes
