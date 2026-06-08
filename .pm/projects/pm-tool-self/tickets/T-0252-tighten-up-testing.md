---
id: T-0252
title: TIghten up testing
type: feature
state: ready
priority: p2
created: 2026-06-05T21:38:52Z
updated: 2026-06-08T18:05:18Z
project: pm-tool-self
section: null
parent: null
children: []
order: 80896
reporter: null
assignee: null
acceptance_criteria:
  - add the various testing criteria and.tighten up the testing parts of both tickets and projects
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2159
    model: claude
    started: 2026-06-05T21:59:16Z
    status: abandoned
    summary: "Reconciling an orphaned claim. This ticket was claimed via the web UI on 5 June but never actually worked — no progress was recorded and no code was written — yet it sat in \"in progress\" ever since, wrongly suggesting work was underway. Returning it to the backlog. The underlying need (tighten our testing rigour: automated checks, edge-case evaluation, cross-impact checks when a shared function changes, and a solid list of things for the human to test) is still wanted — but it's too broad as written and should be broken into concrete, testable pieces before an agent picks it up again."
    ended: 2026-06-08T18:04:47Z
labels: []
attention:
  needed_by: human
  reason: 'Agent run abandoned: Reconciling an orphaned claim. This ticket was claimed via the web UI on 5 June but never actually worked — no progress was recorded and no code was written — yet it sat in "in progress" ever since, wrongly suggesting work was underway. Returning it to the ba'
  since: 2026-06-08T18:04:47Z
---

We need to really tighten up the testing stages as we are able to move so fast. A combination of automatic testing, edge case testing / evaluation, cross checking ie if changing a shared function make sure it isnt used elsewhere eg a conroller function called in project js or ajax or a model function called by another model or controller. Also need. to make sure the user is given extensive list of things to test and check so we move forward with as little risk as possible. Maybe before starting on work identify risk

## Conversation

**2026-06-08 18:05 claude:** **Chased down — never worked; returned to the backlog for re-scoping.**

Claimed on 5 June via the web UI but no work was ever done (no progress, no code); it had been parked in "in progress" since. It's now `ready` + flagged for you.

**Recommendation:** it's too broad to be agent-ready as one ticket (and has no code anchors). Worth splitting into concrete, testable pieces before re-queuing — for example:
- Identify risk *before* starting work (a pre-work risk note on a ticket).
- Cross-impact check: when a shared function/model changes, confirm every other caller is checked.
- Automated-test coverage expectations per change.
- A generated "what to test" checklist surfaced to you at review time.

(Several of these dovetail with T-0268 "richer review handoff" and T-0250 "testing notes with send-back" already in the backlog — might be worth grouping them into a future "testing rigour" sprint.)
