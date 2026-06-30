---
id: T-0496
title: Defining the processes per cohort
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:29:52Z
updated: 2026-06-30T16:36:49Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-014
children: []
order: 17408
reporter: null
assignee: null
acceptance_criteria:
  - "A one-page playbook exists for EACH Stewardship Level (System, Incubation, Account, Strategic) AND each Conversion Process (System follow-up, Quick, In-depth, Lifetime), each defining: owner, qualifying questions, data-capture requirements, the play (what we do & say), cadence, and graduation/exit criteria."
  - Qualifying questions are a BANT-per-level set that, when answered, advance a customer Suggested → Qualified for that level (wired to the T-0497 checklist / 'in the bag' %).
  - Each playbook states what converts a customer from their CURRENT level to the PROPOSED one (the conversion path) and what 'done/confirmed' looks like.
  - Incubation and System-only are fully specified (they are the biggest, least-defined buckets) — System names the steward + automated plays; Incubation names the data gate + template plan + quarterly cadence.
  - Uses the agreed vocabulary (P-0018-phase2-terminology.md); reviewed and signed off by Ben / the workshop.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-terminology.md
    role: the agreed vocabulary the playbooks use
  - path: docs/p0018-sales-segmentation/P-0018-phase2-process-flow.mmd
    role: where playbooks sit in the flow (Steward step)
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: per-level ownership gates (section 5.2) + conversion processes (2.8)
relates:
  - T-0457
  - T-0495
  - T-0497
  - T-0485
  - T-0490
  - T-0491
  - T-0492
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 4
---

## Problem
Once a customer has a Score, Segment and a **suggested Stewardship Level**, we need to know *what to actually do with
them* — per cohort. Ben's PDF "Process descriptions" sketch this; this ticket turns them into usable, consistent playbooks.

## Terminology (the split — see P-0018-phase2-terminology.md)
Two things, two grains, don't merge:
- **Conversion Process** = **quote-level** playbook (System follow-up · Quick · In-depth · Lifetime) — how hard we work
  one enquiry; effort chosen by the customer's potential. Logically per-quote; the customer carries a *default* for
  their level.
- **Stewardship Level** = **customer-level** bucket (System / Incubation / Account / Strategic) — who owns them + the
  cadence. Moving up levels is **Account Elevation** (T-0497), not a Conversion Process.

## Design notes
One playbook per Stewardship Level AND per Conversion Process, each covering: **owner · qualifying questions ·
data-capture requirements · the play (do & say + scripts) · cadence · graduation/exit**. Ben's processes map directly
(System stewardship; Incubation; Account + commission/contract extras; Strategic + Business Development). The spectrum
is automation→human (System→Strategic). Incubation and System-only are fully specified (biggest, least-defined buckets).

## Depends on / relates
T-0457 (engine), T-0495 (validate/qualify), T-0497 (Account Elevation visual), T-0485/T-0490/T-0491 (the play). Levels
+ precise criteria + ownership are workshop decisions (Ben's board questions).