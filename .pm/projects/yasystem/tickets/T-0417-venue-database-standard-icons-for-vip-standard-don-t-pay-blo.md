---
id: T-0417
title: Venue database — standard icons for VIP / Standard / Don't-pay-block venues
type: chore
state: triaged
created: 2026-06-18T08:24:19Z
updated: 2026-06-18T08:28:19Z
project: yasystem
section: null
parent: T-0416
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Ben Leslie
  channel: email
  contact: ben@yahire.com
assignee: null
acceptance_criteria:
  - "The three venue-type icons render per spec: VIP = building-shield, Standard = building, Don't-pay-block = building-circle-xmark, with the specified duotone colours/opacities."
  - The VIP icon is applied to key-account venues; regular venues use the standard icon.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - venue-database
  - ui
attention: null
version: 2
---

## Problem

Standardise the venue-type icons in the venue database to Ben's spec. Supersedes his earlier (2023) suggestions (house-turret / house-medical-circle-exclamation) — "changed my mind."

Part of the venue-database work (parent: T-0416).

## Icons (FontAwesome duotone)

**VIP** — `fa-building-shield`
```html
<i class="fa-duotone fa-solid fa-building-shield" style="--fa-primary-color:#ff4013; --fa-primary-opacity:0.8; --fa-secondary-color:#008cb4; --fa-secondary-opacity:1;"></i>
```

**Standard venue** — `fa-building`
```html
<i class="fa-duotone fa-solid fa-building" style="--fa-primary-color:#e32400; --fa-primary-opacity:.5; --fa-secondary-color:#008cb4; --fa-secondary-opacity:1;"></i>
```

**Don't pay block** — `fa-building-circle-xmark`
```html
<i class="fa-duotone fa-solid fa-building-circle-xmark" style="--fa-primary-color:#ff4013; --fa-primary-opacity:.5; --fa-secondary-color:#006d8f; --fa-secondary-opacity:1;"></i>
```