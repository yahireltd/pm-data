---
id: T-0269
title: "Per-project risk / rigor dial (extend agent-policy: pace + required rigor by risk)"
type: feature
state: triaged
created: 2026-06-05T22:53:29Z
updated: 2026-06-22T16:13:35Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - agent_policy gains a manual rigor level — low | medium | high — set per project in the agent-policy editor (next to commit/push). Set explicitly by the PM, not derived from the charter risks list.
  - "High rigor: pm_complete_run refuses status:completed unless a non-empty, detailed test_plan is provided (enforces the T-0252 standard). Lower levels don't force it."
  - "High rigor raises the review bar: the reviewer is prompted for fuller verification (all acceptance criteria explicitly ticked / a stricter checklist) before approve; low rigor is a lighter touch."
  - The level is returned by pm_get_project / pm_get_ticket (in agent_policy) and shown on the project overview/charter so PMs set + audit it and agents can read it.
  - Builds on agent_policy (T-0247) + branch; does NOT replace Definition-of-Ready (still required to claim) — rigor adds checks on top.
out_of_scope:
  - Claim-time acknowledgement gate (not chosen) and 'visibility-only' banners with no teeth.
  - Per-branch risk variance — project-wide level for now ('down to the branch' deferred).
  - Deriving the level from the charter risks[] — it's a manual setting.
code_anchors:
  - path: schemas/project.schema.json
    symbol: agent_policy.rigor
  - path: cli/src/lib/agent-policy.ts
    symbol: effectiveAgentPolicy (+ rigor)
  - path: mcp-server/src/lib/agent-policy.ts
    symbol: mirror
  - path: mcp-server/src/tools/complete-run.ts
    symbol: gate test_plan by rigor
  - path: web/app/_components/ReviewBanner.tsx
    symbol: stricter review bar
  - path: web/app/_components/AgentPolicyEditor.tsx
    symbol: rigor control
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
collaborators:
  - kind: human
    name: Austin Pickering
version: 1
---

## Problem

The human→agent control plane needs a "how fast vs how careful" dial per project. Today agent_policy (T-0247) only covers commit/push — binary — with no link to risk.

## What we're building (decided 2026-06-08)

A **manual rigor level** on `agent_policy` — **low | medium | high** — set per project in the agent-policy editor (next to commit/push). The PM sets it explicitly (it is NOT derived from the charter risks list).

**What the level drives (the teeth chosen):**
1. **Required test_plan at high rigor** — `pm_complete_run` refuses `status: completed` unless a non-empty, detailed test_plan is provided (enforces the T-0252 standard). Lower levels don't force it.
2. **Stricter review bar at high rigor** — the reviewer is prompted for fuller verification (all acceptance criteria explicitly ticked / a stricter checklist) before approve; low rigor is a lighter touch.

The level is returned by `pm_get_project` / `pm_get_ticket` (in agent_policy) and shown on the project overview/charter so PMs set + audit it and agents can read it. It builds on — does not replace — Definition-of-Ready (still required to claim); rigor adds checks on top.

## Out of scope

- Claim-time acknowledgement gate (not chosen); visibility-only banners with no teeth.
- Per-branch risk variance — project-wide for now ("down to the branch" deferred).
- Deriving the level from the charter `risks[]` — it's a manual setting.
