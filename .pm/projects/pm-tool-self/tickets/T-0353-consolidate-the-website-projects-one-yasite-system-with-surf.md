---
id: T-0353
title: "Consolidate duplicated projects: one Yasite system with surfaces; fold Logistics rollout into Yasystem; rename Yasystem (ADR-039)"
type: chore
state: review
created: 2026-06-10T15:00:58Z
updated: 2026-06-12T01:18:04Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - One yasite project (kind=system) carries all website work with the five surfaces configured
  - T-0346 and T-0347 (and any other website tickets) moved in with correct surface tags, history intact
  - yahire-website and yasite-backend no longer appear in the sidebar/projects list
  - Logistics-route-planning-rollout's 7 tickets (T-0217–T-0223) moved into yasystem with their stream branch pinned on each ticket (T-0352 field); the logistics project retired with a pointer; no ticket ids change
  - Yasystem renamed without 'Main Branch'; its in-flight stream branches (incl. refund-hardening) pinned on tickets
  - No ticket id changes anywhere; all moved tickets open correctly at /tickets/<id>
out_of_scope:
  - Any code changes (this is data work, gated on T-0350/T-0351/T-0352 shipping)
code_anchors:
  - path: web/app/_components/ProjectAssignmentControl.tsx
    symbol: existing move-ticket-between-projects path
  - path: web/app/_actions/projects.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2351
    model: claude-fable-5
    started: 2026-06-11T23:51:04Z
    status: completed
    progress:
      - at: 2026-06-12T01:06:57Z
        note: "Resuming run after pm_move_ticket/pm_update_project tools hot-loaded (T-0359 shipped). State surveyed: T-0346 already in P-0015 with surface=yahire-website; logistics tickets T-0217/T-0218 carry pinned branch SymbioticRoutePlanner from last night. Executing now: move T-0347 → P-0015 (+surface backend), move T-0217–T-0223 → yasystem, retire P-0014/P-0016/P-0007 with pointer goals, set kind=system on yasystem, pin refund-hardening-t0331-stripe-dryrun on T-0320/T-0325."
    ended: 2026-06-12T01:18:04Z
    summary: |-
      **What we did**
      Tidied the duplicated projects into the shape the new taxonomy was built for — a joint effort between Austin (in the web app) and the AI (through the new tools). The websites are now ONE system, "Websites (yasite)", holding both website tickets with their site areas tagged. The Logistics route-planning project's seven tickets moved into Yasystem, each carrying the working branch they're actually built on (PickingSketchSalesDashFriday), so the stream lives inside the system it belongs to. The three leftover shells (Yasite, Yahire Website Backend, Logistics rollout) are retired with pointer notes saying where their work went, and "Yasystem Main Branch" is plain "Yasystem" — the branch is data now, not a name.

      **Why we did it**
      Work was scattered across near-empty duplicate projects, and one project was named after a git branch. Finding anything meant knowing the accidental history; the dashboard and sidebar were cluttered with shells.

      **If we did nothing**
      The duplicates would keep multiplying (a new project per branch or per site), ticket history would stay fragmented, and the new taxonomy (systems, surfaces, branches) would describe a world that didn't match the data.

      **The benefit**
      The sidebar now tells the truth: two real systems (Websites, Yasystem) plus genuine initiatives. Every moved ticket kept its number, history, review state and attention flag — verified one by one. Future agents picking up a route-planning ticket are told the right branch automatically.
    test_plan: |-
      Refresh the browser (the sidebar regroups), then:
      1. Sidebar: SYSTEMS shows Websites (yasite) + Yasystem; INITIATIVES no longer lists Yasite / Yahire Website Backend / Logistics Route Planning rollout — they're in the dimmed CLOSED group, each with a pointer note in its goal.
      2. Websites (yasite) → tickets: T-0346 (done, surface yahire-website) and T-0347 (review, surface backend, attention flag intact, conversation intact — including the move note).
      3. Yasystem → List: T-0217–T-0223 all present with their states unchanged (six review, one in progress) and branch tag PickingSketchSalesDashFriday on each row; T-0217's conversation has the consolidation note.
      4. Open /tickets/T-0218 directly — loads fine (id preserved), Branch field shows the pin, and the agent policy + branch resolve together.
      5. Spot-check Yasystem header: named "Yasystem", kind System (no phase machinery in its nav).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - taxonomy
  - data-migration
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-12T01:18:04Z
version: 8
---

## Problem

Projects are duplicated / misshapen (Austin, 2026-06-11):

- The websites were smeared across three half-empty projects; Austin has now made **P-0015 "Websites (yasite)"** the canonical system (kind=system, repo yahireltd/yasite + branch master, four surfaces configured: yahire-website, chair-hire-london-website, hire-catering-dev, backend) — but **yasite (P-0014, holds nothing after T-0346 moved)** and **yasite-backend (P-0016, T-0347 in review)** still exist as separate initiatives.
- **logistics-route-planning-rollout (P-0007) is really a branch stream of the ERP** (Yasystem): 7 tickets, 6 in review off one commit. A project-per-branch orphans its history when the branch merges (the exact failure ADR-039 exists to prevent).
- The ERP project is named "Yasystem Main Branch", baking a branch into its identity.

## Plan (depends on T-0352 branch-on-ticket being live; T-0351 surfaces is deployed and configured)

1. **P-0015 "Websites (yasite)" is the canonical website system** — already done by Austin (kind, repo, surfaces). Verify the surface keys cover all incoming tickets' areas.
2. Move yasite's (P-0014) and yasite-backend's (P-0016) tickets into P-0015 (ProjectAssignmentControl / actions — note the move CLEARS any surface tag by design), then tag each with the right surface (T-0346 → its site, T-0347 → backend / Site Manager).
3. Retire the two shells P-0014 + P-0016 (delete or mark done with a pointer comment) so the sidebar shows ONE website system.
4. **Fold logistics-route-planning-rollout into yasystem**: move T-0217–T-0223 in, pin their stream branch on each ticket (confirm the branch name with Austin at migration time — the runs reference commit 8b37aef5), then retire the logistics project with a pointer comment. In-review tickets keep their review state and conversations.
5. Rename yasystem: "Yasystem Main Branch" → "Yasystem (ERP)"; kind=system; keep repo + branch=master as the project default; pin in-flight stream branches (e.g. refund-hardening) on their tickets per T-0352.
6. hirecatering stays a surface for now; if/when it becomes a real build-out, spin an initiative project linked via related_projects.

## Caution

This is live-data surgery — do it through the tool (moves preserve ids/history), not by hand-editing files; verify each moved ticket still resolves before deleting shells. Tickets in review keep their attention flags. Moves clear surface tags (T-0351) — re-tag AFTER each move.

## Conversation

**2026-06-12 01:15 claude-code:** Run run-20260611-2351 completed — **What we did:** Tidied the project list down to one project per real system. All website work now lives in a single "Websites (yasite)" project: the backend thumbnails ticket moved in (tagged to the Site Manager surface) joining the breadcrumbs ticket that was already there, and the two leftover shell projects (Yasite, Yahire Website Backend) were retired with a signpost saying where their work went. The seven route-planner tickets moved out of the separate "Logistics Route Planning rollout" project into Yasystem — where that work really belongs — each stamped with the working branch the code sits on (PickingSketchSalesDashFriday, confirmed with Austin), and the logistics project was retired with a signpost too. Yasystem was marked as a long-lived system, and the two in-flight refund tickets were stamped with their refund-hardening working branch. No ticket numbers changed; every moved ticket kept its state, conversation history and review flags.

**Why:** The same work was smeared across three half-empty website projects, and a temporary code branch had been given its own project. That made the project list misleading — you couldn't tell what was real ongoing work versus leftover scaffolding.

**If we did nothing:** The route-planner tickets would have been orphaned in a dead-end project when their branch merges, the duplicate website projects would keep collecting tickets in the wrong places, and anyone glancing at the project list (including stakeholders) would see clutter instead of the actual shape of the work.

**The benefit:** The sidebar now shows the true picture — one website system, one ERP system, the PM tool itself — and every in-flight piece of work records exactly which branch its code lives on, so nothing gets lost when branches merge or sessions change hands.
