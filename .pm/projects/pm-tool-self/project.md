---
id: P-0006
slug: pm-tool-self
name: pm-tool — dogfood improvements
state: active
created: 2026-05-05T17:15:00Z
updated: 2026-07-14T14:26:39Z
owner:
  kind: human
  name: austin
goal: |
  1. Create a project management capability that allows Yahire to identify, design, prioritise and deliver business transformation at a scale and speed that would previously have been impossible.
   
  2. Transform our IT team from reactive developers into AI-augmented transformation professionals who lead, design and deliver meaningful business change.
   
  3. Ensure all information related to projects or possible projects is conveniently sorted, by bringing in our entire workflow including informal chats and small tickets into the same sphere.
success_criteria:
  - A real, large, multi-phase transformation (the Sales programme) has been planned and run in the tool from start to finish.
  - The whole workflow lives in the tool — informal chats and small tickets included — not scattered across email, chat and people's heads.
  - Evidence of completed projects that have been reviewed properly.
  - "Completing projects from end to end — including the people side: planning meetings, user testing, reviews."
  - User feedback on us personally — people notice the quality of the project.
  - Keeping on top of non-project work via the ticketing system.
  - More clarity on roadmap management — anyone can see what's going on and what's next.
  - Clear responsibility on projects for everyone involved — e.g. the end user is on board and understands we need their time.
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
  - We need to deliver business transformation at a scale and speed that wasn't possible before — but we had no capability to identify, design, prioritise and deliver it systematically; change happened ad-hoc.
  - We were reactive developers — taking the brief and building it — rather than leading and designing the change. That caps the value we create, and our own growth.
  - Everything about a project is scattered — formal docs, informal chats, emails, small tickets, what's in someone's head — so no one can quickly see what's going on or what to do next, and things fall through the cracks.
  - Now that agents do the coding well, planning rigor is the bottleneck — bad framing in becomes bad outcomes out, faster than ever.
  - Project alignment fails — people's goals diverge silently and we only notice when work is already drifting.
  - Stakeholder feedback is the slowest part of every project; we lose days waiting for responses that were never structured to be quick.
  - Non-technical PMs and stakeholders see views built for developers and feel locked out.
goals:
  - Run real, large, multi-phase business transformations end to end in the tool — not just small dev tickets (the Sales programme is the first proof).
  - Bring the whole workflow — formal and informal, big and small — into one place, so anyone, including an outside stakeholder, can grasp what's going on and what's next at a glance.
  - Help the team become AI-augmented transformation professionals who lead and design change, with ringfenced time to keep building those skills.
  - Make planning rigorous before code is written — every project starts from a populated charter (problems, goals, scope, risks, measures).
  - Give every role its own surface — a non-technical PM, a developer, and a stakeholder each get a view that fits them, not a developer view.
  - Make stakeholder feedback fast — an async response surface (confirm, feedback, sign-off) so latency drops to hours, not days.
  - Tie people, agents, projects and goals into one coherent picture — the company's wider aim of coherence.
risks:
  - description: Tool tries to be everything (PM + meeting OS + strategy board + agent runner) and ships nothing usable
    severity: high
    mitigation: One thin slice per week; ship in front of real users; cut features that aren't load-bearing within two weeks of landing
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: The director's brief is coherent but big — scope creep is likely without a forcing function
    severity: medium
    mitigation: Time-box each slice; the charter view itself is a forcing function (every project must justify its time budget vs expected outcome)
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: Agents-as-first-class is unique, but the agent loop already works well — over-investing there means under-investing in the human side that's now the bottleneck
    severity: low
    mitigation: Agent-side stays feature-frozen until the human side has shipped (charter, role differentiation, meeting templates)
    status: open
    reviewed_at: 2026-06-02T00:00:00Z
  - description: Bringing the whole workflow into one place makes the tool noisy or overwhelming — signal gets lost among small tickets and chatter
    severity: medium
    mitigation: Strong defaults, filtering, and a fast 'what's going on / what next' overview; keep informal capture lightweight
    status: open
    reviewed_at: 2026-06-17T00:00:00Z
  - description: The team's growth as PMs gets squeezed out by delivery pressure — learning/training is the first thing cut when busy
    severity: medium
    mitigation: Ringfence learning/training time explicitly (Chapter 6 / MS-018) and review it; don't let it be optional
    status: open
    reviewed_at: 2026-06-17T00:00:00Z
success_measures:
  - Every active project has a populated charter (problems, goals, risks, measures) before its first ticket leaves `triaged`.
  - Stakeholders post async responses to >50% of meetings within 24h of the meeting being scheduled.
  - PM, Dev and Stakeholder roles each have a distinct view that's actually used (track which routes each role hits).
  - Time-budget vs actual-spent is visible on every project; projects over 150% of budget surface in Review automatically.
  - At least one large, multi-phase transformation (the Sales programme) has been taken from charter → delivery → handover in the tool.
  - Project context lives in the tool, not in email/chat/heads — measured by how rarely we go outside it to answer 'what's going on / what next?'.
phase: test
scope_in:
  - "Taking projects the whole way: planning → delivery → review → handover → after-care, with the people side (meetings, stakeholders, sign-off, user testing) in the tool."
  - "Running large, multi-phase transformations: systems vs initiatives, milestones-as-phases delivered through sprints (the Sales programme is the first)."
  - Bringing the whole workflow into one place — informal chats, small tickets, 'possible projects', and a fast 'what's going on / what next' overview.
  - "The team's own growth as PMs: ringfenced learning/training and a self-assessment loop."
  - Continuing to sharpen the earlier-chapter foundations — planning rails, role-specific views, fast stakeholder feedback — as real use exposes gaps.
scope_out:
  - Multi-tenant / multi-company support — single team for now
  - Migrating the seeded example projects to the new schema
  - Reworking the agent-run loop — it already works well
  - "Solutions that turn out to be overkill: we capture the idea and the reasoning, but we don't force it into scope now — it's parked for a future revisit. Out-of-scope here means 'not now', not 'lost'."
  - Rebuilding the tools the team already lives in (chat, email, calendar) — we pull the relevant workflow IN and integrate, we don't recreate Slack/Outlook.
workstream_ownership: []
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
cost_of_inaction: Without this, business transformation stays slow and ad-hoc — limited to what we can hold in our heads; we stay reactive developers rather than people who lead and design change; and project information stays scattered, so things keep falling through the cracks and no one — us or our stakeholders — can see the whole picture.
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
  current_workflow: We used to work on projects with very little planning. Ben would capture the problem from the user, come up with an idea to solve it, and ask us devs to build the solution.
  new_workflow: "We now plan projects much more deliberately: capture the problem with the users, propose a solution, hold meetings to decide what's in and out of scope, organise user testing, and take the project from start to finish plus after-care. We're shifting from devs (Claude is now the main dev) to PMs / AI-Ops."
  who_affected:
    - The IT team — moving from building solutions to leading and designing the change
    - End users and stakeholders — now involved earlier (scoping, user testing, sign-off)
  migration_path: phased
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
version: 610
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

## Earlier milestones — what we did and why (archived 17 Jun 2026)

Before restructuring into chapters, the project tracked work as ten finer-grained milestones. They're retired now, but here's what each one actually was, in plain terms — so the history isn't lost. (The detail also lives on in the tickets, decisions and git history.)

**Chapter 1 — Make the work trackable**

- **Laid the foundations.** _What:_ one place to record every project, ticket, decision and meeting, plus the loop where an AI agent picks up a job, does it, and hands it back. _Why:_ there was nowhere to track the work — you couldn't even answer "what is everyone working on right now?" _Benefit:_ every piece of work now has a home and a history you can look back on.

**Chapter 2 — Force good planning**

- **Put the planning rails in.** _What:_ a project now has to have its problem, goals and checkpoints written down before any building starts. _Why:_ now that AI does the coding fast, weak planning is what derails projects. _Benefit:_ vague or half-baked projects can't quietly slip through — you think first.
- **Built the "finish and hand over" half of a project** _(was "back-half lifecycle entities")._ _What:_ the tools for the *later* stages — status updates, formal sign-off, hand-over notes, and logging issues found after launch. _Why:_ projects were easy to start but had no proper finish, so work drifted and never closed cleanly. _Benefit:_ a project can be taken all the way to a tidy finish and a real handover, not just kicked off.
- **Removed the dead-ends.** _What:_ made sure that at every step the tool tells a person clearly what to do next. _Why:_ the guided process had spots where someone could get stuck, unsure of the next move. _Benefit:_ anyone can follow it start to finish without hitting a wall.
- **Gave early ideas the same care as projects** _(pre-projects)._ _What:_ a half-formed idea can be shaped and pressure-tested with the same planning tools before it's promoted to a real project. _Why:_ ideas were either lost or rushed straight into being a project. _Benefit:_ you can test an idea properly before committing real time to it.

**Chapter 3 — Bring the humans in**

- **Made writing in the tool feel normal** _(rich-text editor + a tidy-up of the screens)._ _What:_ a proper document-style editor and a cleaner look. _Why:_ editing was clunky and built for developers. _Benefit:_ writing charters, notes and tickets feels like a normal document, and the tool looks approachable.
- **Brought people and communication in.** _What:_ stakeholders, meetings, and real email + calendar through Microsoft. _Why:_ a project is about people, but that side lived outside the tool. _Benefit:_ the people, the meetings and the emails/invites all live in one place.
- **Put it online.** _What:_ hosted the tool so it can be used from anywhere, and so Claude can drive it remotely. _Why:_ it only ran on one machine. _Benefit:_ anyone on the team can use it from anywhere.
- **Added logins and roles.** _What:_ sign-in, with different views for an admin, a team member, and a stakeholder. _Why:_ everything was open — not safe or tidy for multiple people. _Benefit:_ each person sees the right thing, and outsiders only see what they should.
- **Made communication two-way.** _What:_ emails coming *in* now become tickets and updates, not just emails going out. _Why:_ we could send but not receive inside the tool. _Benefit:_ a reply or an inbound request lands in the right place automatically — nothing falls through the cracks.

## Origins

The pm-tool repo lives in its own world: the `.pm/` directory contains seeded examples that demonstrate the tool against the ya-hire codebase. Work *on the tool itself* — missing views, missing CLI commands, bugs found while using it — had no home; this project is that home. The first ticket delivered the `/activity` view — the README's own pitch ("what is each agent working on?") turned out to apply to the tool itself.

## Conventions for this project

- Code anchors here are **pm-tool-relative paths** (`cli/src/...`), not ya-hire paths.
- Reporters here are typically `claude-code` (system-discovered gap) or `austin` (orchestrator request).
