---
id: T-0191
title: Consolidate phase-2-comms (P-0005) into pm-tool-self; delete duplicate project
type: chore
state: done
created: 2026-06-03T22:23:10Z
updated: 2026-06-03T22:44:14Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - P-0005 (phase-2-comms) deleted; one project (pm-tool-self) remains
  - Its two accepted ADRs migrated into pm-tool-self (ADR-004 stakeholder polymorphism, ADR-005 comms trigger)
  - Milestone MS-005 repointed to ADR-004/005; pm-lint 0 errors
out_of_scope: []
code_anchors:
  - path: .pm/projects/pm-tool-self/decisions
    commit: a167f43
    note: ADR-001/002 migrated to ADR-004/005; P-0005 deleted
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2223
    started: 2026-06-03T22:23:10Z
    status: completed
    ended: 2026-06-03T22:23:10Z
    summary: Retroactive record. Done in a167f43 on 2026-06-02; data migration consolidating P-0005 into pm-tool-self.
    test_plan: pm_list_projects returns only pm-tool-self; ADR-004 and ADR-005 exist in pm-tool-self; pm-lint clean.
labels:
  - backfill
attention: null
---

## Problem
The stakeholders/meetings/comms/Graph work lived in a separate "Phase 2" project (P-0005) which read as a lifecycle phase and showed a confusing done-but-not-closed state.
## Change
Migrated its two accepted ADRs into pm-tool-self (ADR-001→ADR-004 stakeholder polymorphism, ADR-002→ADR-005 comms trigger + inbound cron), repointed milestone MS-005 to ADR-004/005, and deleted P-0005. One project (pm-tool-self) remains.
## Note
Retroactive record of commit `a167f43` (2026-06-02).

## Conversation

**2026-06-03 22:23 claude-code:** Run run-20260603-2223 completed — Retroactive record. Done in a167f43 on 2026-06-02; data migration consolidating P-0005 into pm-tool-self.

---

**2026-06-03 22:44 — you**

done
