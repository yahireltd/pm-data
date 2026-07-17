---
id: T-0609
title: Contract-checking page — build it, retire Website Quotes, add select-all + optimise
type: feature
state: triaged
created: 2026-07-17T07:59:29Z
updated: 2026-07-17T08:02:03Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
  channel: meeting M-010
assignee: null
acceptance_criteria:
  - A dedicated contract-checking page exists and covers the contract-checking use case currently served via Website Quotes.
  - The Website Quotes page (/sales/website-quotes) is retired once contract-checking is live.
  - A select-all control is added.
  - The page is optimised (performance / UX).
out_of_scope: []
code_anchors: []
relates:
  - T-0468
  - T-0467
  - T-0608
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
Sales needs a dedicated **contract-checking page**. Today the **Website Quotes** page (`/sales/website-quotes`) is only kept because it's used for contract checking — once a proper contract-checking page exists, Website Quotes can be retired. While building it, add a **select-all** control and **optimise the page**.

## Context (from M-010, Nathan)
- Build a contract-checking page (Nathan referenced "previous tickets" for this — confirm what he means).
- Then remove the Website Quotes page.
- Add a select-all + general page optimisation.

## Sequencing
- Retiring/removing Website Quotes elsewhere is gated on this page going live: the menu restructure (T-0608) removes it from the menu, and the redundant-pages review (T-0467) lists it as a removal candidate.
