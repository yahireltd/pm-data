---
id: T-0271
title: 'Plain-language user help: a "?" on every screen plus a browsable /help user guide'
type: feature
state: done
created: 2026-06-06T00:24:59Z
updated: 2026-06-06T01:22:34Z
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
  - '[x] Every main screen has a "?" in its header that opens plain-language help written for that exact screen'
  - '[x] A browsable user guide gathers all the help in one place, grouped by area, and opens with a "How we work" overview of the working model'
  - '[x] The user guide and the per-screen "?" are reachable by everyone signed in (including read-only stakeholders), and remain behind the secure sign-in wall — a signed-out person cannot reach them'
  - "[x] All help is in plain, everyday language for the person using the tool: no jargon, no technical file or function names"
  - "[x] A written rule requires a screen's help to be updated in the same change as the screen, so the help never describes a screen that no longer exists"
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
    status: completed
    ended: 2026-06-06T00:47:38Z
    summary: |-
      We added a plain-language help layer to the tool, aimed at the people running the work rather than at developers. Every main screen now has a "Help" button in its header that opens short, jargon-free guidance for that exact screen, and there is a browsable user guide that gathers it all in one place. The guide opens with a one-page overview of how we work: the people run the work and review it; the AI does the building and never marks anything finished by itself — that is always a person's call.

      Why it mattered: until now you couldn't learn the tool from inside the tool. The only in-app documentation was written for developers, so anyone non-technical felt locked out — one of this project's named problems. Without this, every new person would stay dependent on someone explaining each screen by hand.

      The benefit: anyone, including a non-technical colleague, can open any screen, press Help, and understand what it is for and what their part is versus the AI's. The help sits behind the same secure sign-in as everything else, and there is now a standing rule that a screen's help is updated whenever that screen changes, so it can't drift out of date. A follow-up tweak fixed a visual glitch where two help symbols appeared side by side; help now shows as a single tidy button.
    test_plan: |-
      **Verify on the live site (signed in):**

      1. Open a few everyday screens — Dashboard, Tickets, Inbox, Review, Ready, In progress. Each header shows a single tidy **Help** button (and a small hover "?" tooltip — that's the separate quick-hint, kept on purpose). No doubled/tripled "?" symbols.
      2. Click **Help** on any screen — a side panel slides in with plain-language help for that exact screen, ending with a link to the full guide.
      3. Open the user guide from the sidebar (**Help**) or the top bar (**help**). It lists every screen grouped by area and opens with the **How we work** overview.
      4. Open a project and switch tabs (Charter, Sprints, Status, Board, Phase…). The project header's **Help** button content changes to match the current tab.
      5. Open a single ticket, a meeting, and a pre-project — each has its own Help.

      **Security check (the important one):**
      6. Sign out (or use a private window) and try to visit the guide directly. You should be bounced to the sign-in page — the help must never be readable while signed out.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Updated the agent ruleset and the in-app developer wiki to require each screen's plain-language help be updated in the same change as the screen, so help can't drift from the UI.
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

## Conversation

**2026-06-06 00:47 claude-code:** Run run-20260606-0026 completed — We added a plain-language help layer to the tool, aimed at the people running the work rather than at developers. Every main screen now has a "Help" button in its header that opens short, jargon-free guidance for that exact screen, and there is a browsable user guide that gathers it all in one place. The guide opens with a one-page overview of how we work: the people run the work and review it; the AI does the building and never marks anything finished by itself — that is always a person's call.

Why it mattered: until now you couldn't learn the tool from inside the tool. The only in-app documentation was written for developers, so anyone non-technical felt locked out — one of this project's named problems. Without this, every new person would stay dependent on someone explaining each screen by hand.

The benefit: anyone, including a non-technical colleague, can open any screen, press Help, and understand what it is for and what their part is versus the AI's. The help sits behind the same secure sign-in as everything else, and there is now a standing rule that a screen's help is updated whenever that screen changes, so it can't drift out of date. A follow-up tweak fixed a visual glitch where two help symbols appeared side by side; help now shows as a single tidy button.

---

**2026-06-06 01:11 — you**

very good work on the ticket and detialed close all good!

**2026-06-06 01:22 claude-code:** For the record: the design thinking behind this work is now written up as a tech-session, **TS-013**. It captures why we used one small help file per screen, the single reusable "Help" button (and how it covered all the project tabs from one change), the sign-in rules, how the thirty screens were generated from the real code and fact-checked, and two lessons — that one clearly-labelled button beats a second "?" symbol next to the existing tooltip, and that the help files can be checked locally before deploy.

When you close this ticket, you can point the "tech-session" part of the records check at TS-013 if you'd like them linked (I'd recorded it as "none-needed" at the time, before writing the session up).
