---
id: T-0227
title: Revert to / see previous phases
type: feature
state: done
priority: p2
created: 2026-06-05T13:49:51Z
updated: 2026-06-05T21:24:03Z
project: pm-tool-self
section: null
parent: null
children: []
order: 60416
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] A way to revert the phase to the pevious"
  - "[x] a way to view previous phases (link the phase buttons)"
out_of_scope: []
code_anchors:
  - path: web/app/_components/PhaseCommandCenter.tsx
  - path: web/app/projects/[slug]/phase/page.tsx
  - path: web/app/_actions/projects.ts
    symbol: revertPhase
  - path: cli/src/lib/playbook.ts
    symbol: previousPhase
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1350
    model: claude
    started: 2026-06-05T13:50:39Z
    status: completed
    summary: "Updated after your send-back — you can now actually revert, from the phase you navigate to. Open an earlier phase in the rail and click \"Revert the project to this phase\" (e.g. while viewing Build, send a Test-phase project back to Build); the current phase also has a \"Revert to <previous>\" button. A confirmation makes the key point clear: reverting only moves the phase marker back — it deletes nothing. All the later stages' work (decisions, milestones, meetings, status notes) stays on the project, so advancing forward again loses nothing. Viewing earlier phases (read-only) works as before."
    ended: 2026-06-05T18:13:15Z
    test_plan: "On a project past Intake: click an earlier phase in the top rail to view it, then click \"Revert the project to this phase\" and confirm — the project's phase moves back to that one (e.g. Test → Build) and every later-stage item (decisions/milestones/meetings/status) is still present. The current-phase \"Revert to <previous>\" also reverts one step. At Intake there is no revert. Confirm the confirm-bar text states no data is lost."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

once we progress to the next phase we can not go back, We can also not see the previous phases

## Conversation

**2026-06-05 18:03 claude:** Run run-20260605-1350 completed — Projects move through phases (Intake → Planning → Build → …). Until now you could only go forward and couldn't look back. Now the phase buttons along the top are clickable — click any earlier phase to see its checklist read-only, with a clear "you're viewing the X phase — back to current" banner. And there's a "Revert to <previous phase>" button (with a confirm step) that steps the project back a phase if you advanced too soon. Reverting is treated as a deliberate step back, so it doesn't trip the phase gates or leave a "forced" mark on the project.

---

**2026-06-05 18:13 — you**

now works

**2026-06-05 21:24 claude:** **What we did** — You can now step a project back to an earlier phase, and you can click any earlier phase along the top to look at it read-only.

**Why** — Once a project moved forward to the next phase, there was no way to go back, and no way to look at the phases you'd already passed.

**What would have happened if we did nothing** — Anyone who advanced a project too soon would be stuck in the wrong phase, with no way to correct the mistake or even review earlier stages.

**The benefit** — Reverting only moves the phase marker back and deletes nothing, so all the later work stays put and moving forward again loses nothing. A clear confirmation makes that safe. This helps anyone who advances a project ahead of schedule and wants to set it right.
