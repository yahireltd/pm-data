---
id: T-0447
title: Move display settings (text size + avatar colour) from /me to the Settings page
type: feature
state: ready
created: 2026-06-19T09:38:42Z
updated: 2026-06-22T16:14:10Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt Turu
  channel: email
  contact: zsolt@yahire.com
assignee:
  kind: human
  name: austin@yahire.com
acceptance_criteria:
  - The Text size control and the Avatar colour control both render on the Settings page (app/settings/page.tsx), grouped under a 'Display' section.
  - The /me ('My work') page no longer shows the 'Display settings' section — the work page is purely work.
  - "Both controls behave exactly as before after the move: text size still persists per-browser and scales the UI; avatar colour still saves to config and shows the same colour for every viewer."
  - No dead imports, unused components, or empty layout containers are left behind on /me.
  - Navigating to Settings shows the controls in a sensible position; no visual regressions on either page.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - facelift
  - ui
attention: null
version: 5
collaborators:
  - kind: human
    name: Austin Pickering
---

## Problem

The /me ("My work") page carries a "Display settings" section (Text size + Avatar colour) at the bottom. These are one-off personal preferences, not work — a user sets them once and forgets them. They don't belong on the work dashboard; they should live on the Settings page.

## Context

- /me renders FontScaleControl (Text size, T-0305) and AvatarColorControl (Avatar colour, T-0318) inside a "Display settings" block near the bottom of app/me/page.tsx (~line 232+).
- A Settings page already exists at app/settings/page.tsx.
- Text size is browser-local (per-browser; scales the UI via the layout font-scale script). Avatar colour is server-side/shared (stored in config.yml `avatar_colors`, so everyone sees the same colour for a person).

## Proposed approach

- Move both controls into app/settings/page.tsx under a "Display" section.
- Remove the "Display settings" block from /me so the work page stays focused on work.
- Keep the components themselves unchanged (FontScaleControl, AvatarColorControl) — just relocate where they render and pass through the same props (the avatar control needs the user's display name + current colour, already resolved on /me today; replicate that resolution on the settings page).

## Out of scope

- Any behaviour change to the controls themselves.

## Key files

app/me/page.tsx (remove the Display settings block), app/settings/page.tsx (add the Display section), app/_components/FontScaleControl.tsx, app/_components/AvatarColorControl.tsx.
