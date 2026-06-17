---
id: P-0006
slug: pm-tool-self
name: pm-tool — dogfood improvements MAIN BRANCH
state: active
created: 2026-05-05T17:15:00Z
updated: 2026-06-17T17:43:07Z
owner:
  kind: human
  name: austin
goal: |
  1. Create a project management capability that allows Yahire to identify, design, prioritise and deliver business transformation at a scale and speed that would previously have been impossible.
   
  2. Transform our IT team from reactive developers into AI-augmented transformation professionals who lead, design and deliver meaningful business change.
   
  3. Ensure all information related to projects or possible projects is conveniently sorted, by bringing in our entire workflow including informal chats and small tickets into the same sphere.
success_criteria:
  - Evidence of completed projects that have been reviewed properly
  - Completing projects from end to end
  - Dealing with the people side - eg planning meetings / user testing / reviews
  - User feedback on us personally - people notice the quality of the project
  - Keeping on top of non project work via the ticketing system
  - More clarity on the roadmap managements
  - Clear responsibility on projects for everyone involved e.g  the end user to be on board and understand we need there time
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
  - project management
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
  - "Solutions that turn out to be overkill: we capture the idea and the reasoning, but we don't force it into scope now — it's parked for a future revisit. Out-of-scope here means 'not now', not 'lost'."
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
  - name: Zsolt
    channel: email
    contact: zsolt@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-03T14:03:52Z
    role: Dev
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
repo_url: https://github.com/yahireltd/pm-tool
branch: master
version: 201
---

# pm-tool — dogfood improvements

## The why and the what

The **why** of this project is the three transformational goals (in the project's goals):

1. A project-management capability that lets Yahire identify, design, prioritise and deliver business transformation at a scale and speed that wasn't possible before.
2. Turning the IT team from reactive developers into AI-augmented transformation professionals who lead, design and deliver real business change.
3. Bringing the whole workflow — even informal chats and small tickets — into one sphere.

Everything else — the views, the gates, the entities, the features — is the **content**: the tangible things that might add up to those goals, not the reason for them. When deciding whether something belongs, judge it against the *why*, not the feature list. _(Framing from Ben, Jun 2026.)_

## The story — chapters

The milestones bundle the work into chapters — the story of the tool, from where we started to where we're going. Each milestone (MS-013…MS-018) is a chapter; the work inside it is delivered through sprints and tickets. (Target dates, and which chapters are "hit", are for the team to set; the current push's `go_live_target` is 30 Jun 2026.)

1. **Make the work trackable** _(done)_ — every piece of work has a home and a history; the agent loop runs end to end; the filesystem is the single source of truth.
2. **Force good planning** _(done)_ — charter-before-build, phase gates that bite, close-the-loop; pre-projects for intake.
3. **Bring the humans in** _(mostly done)_ — meetings, stakeholders, two-way comms (email + calendar), hosting, login + roles, async stakeholder feedback, friendlier non-dev views.
4. **Handle real scale** _(in progress)_ — systems vs initiatives, the Sales transformation as the first real big one, milestones-as-phases delivered through sprints (ADR-039 / ADR-040 / ADR-041).
5. **Everything in one sphere** _(next)_ — informal chats, small tickets and possible projects all in one place; the fast "what's going on / what next" overview; people, agents, projects and goals aligned.
6. **Us as PMs** _(ongoing)_ — ringfenced learning/training, a self-assessment on our weakest areas, the real shift from reactive devs to AI-augmented transformation professionals.

## Earlier milestones (archived 17 Jun 2026)

Before restructuring into chapters, the project tracked work as ten finer-grained milestones. They were retired in favour of the chapter model above — but the work itself lives on in the project's tickets, ADRs, and the `pm-data` git history (full acceptance criteria are recoverable there). For the record, mapped to their chapter:

- **Ch1:** Layer 0 — Foundation _(hit)_
- **Ch2:** v2 hard-guidance spine + makeover foundation _(hit, 29 May)_ · v2 back-half lifecycle entities _(planned)_ · Guided lifecycle has no human dead-ends _(planned)_ · Pre-projects reach parity with projects _(hit, 5 Jun)_
- **Ch3:** v2 WYSIWYG editor + authoring surfaces + UI sweep _(hit)_ · Phase 2 — stakeholders, meetings, comms, MS-Graph integration _(hit, 6 May)_ · Hosted & remotely drivable _(hit, 3 Jun)_ · Multi-user access control _(hit, 3 Jun)_ · Two-way comms _(hit, 3 Jun)_

## Origins

The pm-tool repo lives in its own world: the `.pm/` directory contains seeded examples that demonstrate the tool against the ya-hire codebase. Work *on the tool itself* — missing views, missing CLI commands, bugs found while using it — had no home; this project is that home. The first ticket delivered the `/activity` view — the README's own pitch ("what is each agent working on?") turned out to apply to the tool itself.

## Conventions for this project

- Code anchors here are **pm-tool-relative paths** (`cli/src/...`), not ya-hire paths.
- Reporters here are typically `claude-code` (system-discovered gap) or `austin` (orchestrator request).
