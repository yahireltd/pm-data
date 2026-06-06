---
id: T-0271
title: 'Plain-language user help: a "?" on every screen plus a browsable /help user guide'
type: feature
state: in_progress
created: 2026-06-06T00:24:59Z
updated: 2026-06-06T00:26:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Every main screen has a "?" in its header that opens plain-language help written for that exact screen
  - A browsable user guide gathers all the help in one place, grouped by area, and opens with a "How we work" overview of the working model
  - The user guide and the per-screen "?" are reachable by everyone signed in (including read-only stakeholders), and remain behind the secure sign-in wall — a signed-out person cannot reach them
  - "All help is in plain, everyday language for the person using the tool: no jargon, no technical file or function names"
  - A written rule requires a screen's help to be updated in the same change as the screen, so the help never describes a screen that no longer exists
out_of_scope:
  - Per-screen help for the developer wiki itself (it stays the developer-facing reference)
  - Translating the help into other languages
  - Video or interactive walkthroughs
code_anchors:
  - path: web/app/help
  - path: web/app/_components/HelpLauncher.tsx
  - path: web/app/_components/ui/PageHeader.tsx
    symbol: PageHeader
  - path: web/app/layout.tsx
  - path: AGENTS.md
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260606-0026
    model: claude-opus-4-8
    started: 2026-06-06T00:26:34Z
    status: in_progress
labels: []
attention: null
---

## Problem

People using the tool to run the work — a project manager, or a colleague new to it — kept meeting screens and documentation written for developers. The only in-app documentation was the developer wiki (architecture, deploy, internals), which doesn't explain how to *use* the tool. New, non-technical people felt locked out, which is one of this project's named problems.

## What we did

Added a plain-language help layer aimed at the person running the work. Every main screen now has a "?" in its header that opens short, jargon-free help for that exact screen. There is also a browsable user guide that gathers all of it in one place, opening with a one-page "How we work" overview. That overview spells out the working model: the people run the work (decide what matters, scope it, review, test, watch risk and security); the AI does the building and hands work back; and the AI never marks anything "done" — that is always a person's call.

## Why it mattered / what would have happened otherwise

You couldn't learn the tool from inside the tool. Help existed only as brief hover-tooltips plus the developer wiki. Without this, every new user (and every stakeholder) stays dependent on someone explaining each screen by hand, and the tool keeps feeling developer-only.

## The benefit

Anyone — including a non-technical colleague — can open any screen, press "?", and understand what it's for and what their part is versus the AI's. The help lives with the code, sits behind the same secure sign-in as everything else, and there is now a written rule that a screen's help is updated whenever the screen changes, so it can't drift out of date.