---
id: P-0006
slug: pm-tool-self
name: pm-tool — dogfood improvements
state: active
created: 2026-05-05T17:15:00Z
updated: 2026-06-05T16:52:55Z
owner:
  kind: human
  name: austin
goal: |
  The pm-tool repository is itself an active project. This is the place to
  track the tool's own improvements: missing views, missing CLI commands,
  bugs we hit while dogfooding. Keeping a meta-project means the tool is
  visible to itself — every fix gets a ticket, agent run, and review gate.
success_criteria:
  - Every dogfood-discovered gap has a ticket here, not just a chat message
  - At least one feature delivered end-to-end via the agent loop (claim → run → complete → review)
  - The /activity view answers "what has this agent done lately" without scrolling logs
parent_project: null
related_projects: []
parent_ticket: null
code_surfaces:
  - cli/src/
  - mcp-server/src/
  - web/app/
  - linter/src/
key_decisions: []
labels:
  - meta
  - dogfood
order: 5120
problems:
  - Project alignment fails — people's goals diverge silently and we only notice when work is already drifting
  - Stakeholder feedback is the slowest part of every project; we lose days waiting for responses that were never structured to be quick
  - Now that agents do the coding well, planning rigor is the bottleneck — bad framing in becomes bad outcomes out, faster than ever
  - Non-technical PMs and stakeholders see views built for developers and feel locked out
goals:
  - Make planning rigorous before code is written — every project starts with a populated Charter (problems, goals, risks, measures)
  - Differentiate views so a non-technical PM, a Dev, and a Stakeholder each have a surface that fits their role
  - Give stakeholders an async response surface on meetings (confirm, feedback, sign-off) so feedback latency drops to hours not days
  - Connect the tool's purpose to the company's wider aim of coherence — alignment of people, agents, projects, and goals in one place
risks:
  - description: Tool tries to be everything (PM + meeting OS + strategy board + agent runner) and ships nothing usable
    severity: high
    mitigation: One thin slice per week; ship in front of real users; cut features that aren't load-bearing within two weeks of landing
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: The director's brief is coherent but big — scope creep is likely without a forcing function
    severity: medium
    mitigation: Time-box each slice; the Charter view itself is a forcing function (every project must justify its time budget vs expected outcome)
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: Yii vs Next.js debate stalls UI progress while we re-litigate the stack
    severity: medium
    mitigation: Build the next human-side feature in both stacks if needed; let velocity-on-real-feature decide, not theoretical preference
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: Agents-as-first-class is unique to this tool, but agent loop already works well — over-investing there means under-investing in the human side that's now the bottleneck
    severity: low
    mitigation: Agent-side is feature-frozen until human-side has shipped Charter + role differentiation + meeting templates
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
success_measures:
  - Every active project in `.pm/projects/` has a populated Charter (problems, goals, risks, measures) before its first ticket leaves `triaged`
  - Stakeholders post async responses to >50% of meetings within 24h of the meeting being scheduled
  - PM, Dev, and Stakeholder roles each have a distinct view that's actually used (track which routes each role hits)
  - Time-budget vs actual-spent visible on every project page; projects exceeding 150% of budget surface in Review automatically
phase: test
scope_in:
  - The guided phase-gated lifecycle engine driven by .pm/playbook.yml
  - Milestone + status-note entities and charter-completion fields
  - The web phase "command center" view
  - Meetings-per-phase + reminders (human/comms layer)
  - The UI redesign onto a proper design-token system
scope_out:
  - Multi-tenant / multi-company support — single team for now
  - Migrating the seeded example projects to the new schema
  - Reworking the agent-run loop — it already works well
workstream_ownership:
  - workstream: Foundation, engine, and surfaces
    owner: claude
  - workstream: Product direction, review, and sign-off
    owner: austin
time_plan:
  rows:
    - category: Project build
      periods:
        - period: 2026-may
          budgeted_hours: 20
          current_estimate_hours: 20
          actual_hours: 25
    - category: Integration
      periods:
        - period: 2026-may
          budgeted_hours: 15
          current_estimate_hours: 15
          actual_hours: 30
    - category: Admin & meetings
      periods:
        - period: 2026-may
          budgeted_hours: 50
          current_estimate_hours: 30
          actual_hours: 50
  periods:
    - 2026-may
reforecast_records:
  - at: 2026-05-29T15:26:35Z
    reason: Wauting on user testing for longer than expected
    period: 2026-may
    to_hours: 30
    by: me
cost_of_inaction: |
  Projects will not be done in the best way possible
stakeholders:
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: you
    added_at: 2026-06-02T18:52:39Z
    role: PM
    notify_on:
      - state_change
      - meeting_held
      - comment_added
      - outcome_recorded
      - assigned
      - meeting_scheduled
  - name: TEst
    channel: email
    contact: test@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-03T14:03:52Z
workflow_impact:
  current_workflow: "We used to work on projects with very little planning. Ben would capture the problem from the user- come up with an idea to solve it and ask us devs to create the solution. "
  new_workflow: "We now want to plan projects a lot more meticulously. We are to capture the problem with the users. Propose a solution, hold meetings to decide what is in scope and what is not. Organise user testing etc. Get the project over the line from start to finish + aftercare. We are shifting from devs (claude is now the main dev) to PM's /AI Ops "
  who_affected:
    - Developers
    - austin@yahire.com
team:
  - austin@yahire.com
adoption_readiness:
  audience:
    - IT Dept
  training:
    - We will train by using the tool
  success_metrics:
    - success is usage of the tool and better project outcomes
  feedback_channels:
    - this tool
environments:
  - name: Local
    purpose: Dev of the tools code
    divergences:
      - no remote MCP server
  - name: Prod
    purpose: Real usage of the tool
    url: support.yahire.com
go_live_target: 2026-06-30
time_budget_hours: 120
---

# pm-tool — dogfood improvements

## Overview

The pm-tool repo lives in its own world: the `.pm/` directory contains
seeded examples (agency-rates, sales-dashboard, etc) that demonstrate the
tool against the ya-hire codebase. But work *on the tool itself* —
missing views, missing CLI commands, bugs found while using it — has had
no home. This project is that home.

## Why now

While exercising the agent loop end-to-end (Austin orchestrates, Claude
claims and runs), the README's pitch — "you can't answer 'what is each
agent currently working on?'" — turned out to apply to the tool itself.
There's no `/activity` view, no `pm activity` CLI. So the first ticket
here delivers exactly that, and the project hosts whatever else surfaces.

## Conventions for this project

- Code anchors here are **pm-tool-relative paths** (`cli/src/...`),
  not ya-hire paths. The default `code_root` in `.pm/config.yml` points
  at ya-hire, so the linter will currently flag these — that's a known
  v1.1 gap (per-project `code_root` override).
- Reporters here are typically `claude-code` (system-discovered gap)
  or `austin` (orchestrator request).
