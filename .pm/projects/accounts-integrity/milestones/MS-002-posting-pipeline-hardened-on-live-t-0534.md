---
id: MS-002
slug: posting-pipeline-hardened-on-live-t-0534
title: Posting pipeline hardened on live (T-0534)
project: accounts-integrity
state: planned
order: 2048
created: 2026-07-10T21:16:32Z
updated: 2026-07-10T21:16:32Z
acceptance_criteria:
  - Branch merged to master and deployed; migration run; /xero/login on live
  - Run-post mutex, self-heal, backstops, DB token store active in production
  - Daily cron 06:00 running with digest received; canary posting run clean
  - Linked-email data fixes applied
  - Sandbox refresh cron re-enabled
slip_records: []
target_date: 2026-07-24
version: 1
---

# Posting pipeline hardened on live (T-0534)

## Acceptance criteria

## Notes
