---
id: MS-005
slug: phase-2-stakeholders-meetings-comms-ms-graph-integ
title: Phase 2 — stakeholders, meetings, comms, MS-Graph integration (shipped)
project: pm-tool-self
state: hit
order: 5120
created: 2026-05-29T13:37:54Z
updated: 2026-06-02T16:09:53Z
acceptance_criteria:
  - A stakeholder added to a ticket with notify_on receives a notification when that ticket changes state
  - Meetings are first-class — planned with agenda + stakeholders; outcomes recorded post-meeting can promote to follow-up tickets
  - Inbound support email auto-creates an inbox ticket via Microsoft Graph (cron poll), reporter populated from the From field
  - Stakeholders see ticket updates by email without logging into pm-tool
  - Comms layer is channel-generic (adding a new channel is cheap)
  - Outbound email sends via MS-Graph with credentials fetched from AWS Secrets Manager at runtime (never on disk)
slip_records: []
target_date: 2026-05-06
phase: build
---

# Phase 2 — stakeholders, meetings, comms, MS-Graph integration (shipped)

Earlier build phase, now shipped. Originally scoped as a separate project (`phase-2-comms`, P-0005); that project
was consolidated into this master pm-tool project and removed. This milestone
records the shipped work; the detailed decisions live in ADR-004 (stakeholder
polymorphism) and ADR-005 (comms trigger + inbound cron).

## Acceptance criteria

Captured in frontmatter above — all met.

## Notes

The outbound email pipeline is proven (real test email sent, Graph 202). The
one piece still open is the time-based reminders poller (see docs/HANDOVER.md).
