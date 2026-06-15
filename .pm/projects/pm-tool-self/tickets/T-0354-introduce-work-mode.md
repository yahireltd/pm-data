---
id: T-0354
title: Work Mode — personal toggle that hides your private projects from your own view
type: feature
state: done
priority: p2
created: 2026-06-10T15:08:48Z
updated: 2026-06-15T13:19:22Z
project: pm-tool-self
section: null
parent: null
children: []
order: 98304
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A 'Work mode' toggle in the top bar, persisted per browser (cookie, so server components can filter), hides every private project you can see from all aggregate surfaces: sidebar groups, dashboard, /projects, /me, search, dev-tickets, and the pickers (promote / move-to-system / project assignment)"
  - "[x] Tickets belonging to hidden private projects disappear from cross-project lists (tickets, review, ready, in-progress, activity) while work mode is on"
  - "[x] A clear always-visible indicator shows work mode is ON; one click turns it off and everything returns"
  - "[x] Enforced at the visibleProjects()/visibleTickets() chokepoint (T-0348) — no per-page filtering that can drift"
  - "[x] Colleagues' visibility is UNCHANGED: private projects are already hidden from other users server-side (ADR-035/T-0290/T-0348) — work mode is a personal de-clutter/screen-share filter, not a security boundary; direct URLs to your own private projects still open"
out_of_scope:
  - Changing who can see private projects (already enforced)
  - Per-project mute/pin (this is one global personal toggle)
  - Hiding private MEETINGS/decisions outside project-scoped surfaces (follow-up if needed)
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: visibleProjects / visibleTickets — the T-0348 chokepoint; add the work-mode cookie filter here
  - path: web/app/_components/TopNav.tsx
    symbol: work-mode toggle + ON indicator
  - path: web/app/_components/RoleSwitcher.tsx
    symbol: pattern for a personal view toggle
  - path: web/app/projects/[slug]/layout.tsx
    symbol: pm_last_project cookie — the cookie read/write pattern for server components
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0143
    model: claude-fable-5
    started: 2026-06-12T01:43:27Z
    status: completed
    progress:
      - at: 2026-06-12T01:49:07Z
        note: "Work Mode implemented: an eye-toggle in the top bar (amber while on) hides your private projects from every list, menu, count and picker — enforced at the single access chokepoint all those surfaces already share, plus the My Work page which lists differently. It's a personal view filter via a browser cookie: colleagues' visibility is untouched (they never saw private projects), direct links still open, and turning it off restores everything instantly. Dashboard help documents it. Typecheck clean; adversarial review of the access-layer change running before commit."
    ended: 2026-06-12T02:08:00Z
    summary: |-
      **What we did**
      Added Work Mode: an eye button in the top bar that hides your private projects from your own view — every list, menu, count and picker forgets them while it's on, and the button glows amber so you always know. One click brings everything back. Crucially, if you deliberately open a hidden project (or one of its tickets or meetings) by direct link, the page still works fully — review caught and fixed three places where it would have degraded. The filter also switches itself off while you're previewing the app as someone else, so preview keeps answering "what do they see" truthfully.

      **Why we did it**
      Austin has private projects (like the stock predictions engine) that colleagues already can't see — but he could, everywhere, cluttering his work view and risking exposure when screen-sharing at the office.

      **If we did nothing**
      Personal projects would keep sitting in every list and menu — visual noise daily, and a privacy slip waiting to happen on any shared screen.

      **The benefit**
      One click separates work from personal: a clean shared-screen-safe view at the office, everything back the moment you want it. Colleagues' view is untouched either way.
    test_plan: |-
      After `pm-deploy` (commit 228333f — also carries the collapsed Closed sidebar group and the ticket branch picker) and a refresh:

      **Happy path**
      1. Top bar: a grey eye button labelled "Work mode". Click it → it turns amber, and Stock Predictions Engine vanishes from: the sidebar, the dashboard, /projects, /me, the Cmd+K quick-add picker, and the tickets list (its tickets gone too).
      2. Click it again → everything returns instantly.

      **The review catches — worth checking**
      3. With work mode ON, open the hidden project by direct URL (/projects/stock-predictions-engine) → the project opens and works: its sprint board resolves ticket names (no "(unknown ticket)"), its tickets open with repo links, branch inheritance hint, and surface picker intact, and a project meeting still shows its stakeholder register.
      4. With work mode ON, use "Preview as" (Settings) to view as a colleague → the work-mode button disappears and the preview shows that person's TRUE visibility (work mode doesn't subtract from it). Exit preview → button returns, still on.

      **Boundaries**
      5. Work mode is per browser (a cookie) — your phone/other browser is unaffected.
      6. Colleagues' views are unchanged either way (they never see private projects).
      7. Agents via MCP are deliberately unaffected — they keep working private-project tickets.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Dashboard help documents the toggle (private projects had no prior help mention — pre-existing gap noted).
labels: []
attention: null
version: 14
---

## Problem

Austin has private projects (e.g. stock-predictions-engine) that are correctly hidden from colleagues (ADR-035: `private: true` + invited, enforced server-side since T-0348) — but the OWNER still sees them everywhere: sidebar, dashboard, lists. That's clutter in the work view, and a screen-share risk in the office.

## Design

A personal "Work mode" toggle:

- Lives in the top bar; state in a cookie (`pm_work_mode`) — a cookie, not localStorage, because the Sidebar/dashboard are SERVER components and the filter must apply at render.
- When ON, the `visibleProjects()` / `visibleTickets()` access chokepoint additionally drops projects with `private: true` (and their tickets) for the current viewer. One filter point — every surface that already routes through it (T-0348) inherits the behaviour for free.
- Always-visible ON indicator; one click off.
- NOT a security boundary: colleagues never saw these projects anyway. Direct URLs to your own private project still work — it's your data, work mode just keeps it out of the shared-screen furniture.

Fits the T-0358 nav work (the new Systems/Initiatives/Closed groups all derive from the same visible set, so they filter for free).

## Conversation

**2026-06-12 02:08 claude-code:** Run run-20260612-0143 completed — **What we did**
Added Work Mode: an eye button in the top bar that hides your private projects from your own view — every list, menu, count and picker forgets them while it's on, and the button glows amber so you always know. One click brings everything back. Crucially, if you deliberately open a hidden project (or one of its tickets or meetings) by direct link, the page still works fully — review caught and fixed three places where it would have degraded. The filter also switches itself off while you're previewing the app as someone else, so preview keeps answering "what do they see" truthfully.

**Why we did it**
Austin has private projects (like the stock predictions engine) that colleagues already can't see — but he could, everywhere, cluttering his work view and risking exposure when screen-sharing at the office.

**If we did nothing**
Personal projects would keep sitting in every list and menu — visual noise daily, and a privacy slip waiting to happen on any shared screen.

**The benefit**
One click separates work from personal: a clean shared-screen-safe view at the office, everything back the moment you want it. Colleagues' view is untouched either way.

---

**2026-06-15 13:19 — you**

working
