---
id: MS-004
slug: v2-back-half-lifecycle-entities
title: "v2: back-half lifecycle entities"
project: pm-tool-self
state: planned
order: 4096
created: 2026-05-29T13:27:52Z
updated: 2026-05-29T13:27:52Z
acceptance_criteria:
  - Defect lifecycle (reported → confirmed → … → closed) + lint rule
  - Incident + Handover entities and a real Hypercare exit gate
  - Time-plan matrix (hours, not money) + reforecast / replan records
  - ADR variants (dependency / integration / deployment / change-control) + their lint rules + multi-lens reviews
slip_records: []
---

# v2: back-half lifecycle entities

## Acceptance criteria

_What must be true for this milestone to count as hit._

- Defect lifecycle states (`reported → confirmed → … → closed`) with commands and a lint rule.
- Incident and Handover entities, and a real Hypercare exit gate (off the current placeholder).
- Time-plan matrix expressed in hours (not money), plus dated reforecast / replan records.
- ADR template variants (dependency / integration / deployment / change-control), each with its own lint rule and multi-lens reviews.

## Notes

W3 of the v2 push — the back-half lifecycle entities.
