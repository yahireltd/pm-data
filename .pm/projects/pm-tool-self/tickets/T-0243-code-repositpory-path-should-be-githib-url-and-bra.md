---
id: T-0243
title: code repositpory path should be githib url and branch
type: feature
state: review
priority: p2
created: 2026-06-05T18:24:02Z
updated: 2026-06-05T20:22:23Z
project: pm-tool-self
section: null
parent: null
children: []
order: 75776
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - code path should be github url
  - branch should be needed
  - auth may need to be considered to view branches (read only should be used for this)
out_of_scope: []
code_anchors:
  - path: schemas/project.schema.json
    symbol: repo_url
  - path: cli/src/types.ts
  - path: web/app/_lib/github.ts
  - path: web/app/_actions/projects.ts
    symbol: listRepoBranches
  - path: web/app/_components/RepoEditor.tsx
  - path: web/app/projects/[slug]/overview/page.tsx
  - path: web/app/tickets/[id]/page.tsx
  - path: linter/src/rules/anchors.ts
    symbol: projectRepoUrl
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2022
    model: claude-opus-4-8
    started: 2026-06-05T20:22:09Z
    status: completed
    ended: 2026-06-05T20:22:23Z
    summary: |-
      A project's "Code repository" is now a GitHub repo URL + branch instead of a local folder path — so it's clear exactly which repo and branch a project (including the new system projects) is working on. On a project's Overview you paste the GitHub URL and set the branch; click "Load branches" to pick it from the live list, which needs a read-only GitHub token on the server (without one, you just type the branch — everything else still works). A ticket's code references (code anchors) now become clickable links that open the file on GitHub at that branch. The old local path still exists for any project that prefers an on-disk check, but GitHub is now the default way to point at code.

      Heads-up for it to light up fully: a read-only GitHub token (fine-grained, Contents + Metadata read on the org repos) needs to be set as GITHUB_TOKEN on the server. Until then the branch is free-text. Want me to /schedule a reminder to wire that token, or will you set it?
    test_plan: "On a project Overview: paste a GitHub repo URL (e.g. https://github.com/yahireltd/pm-tool) — it's accepted; a bad URL is rejected with a clear message. With GITHUB_TOKEN set on the server, \"Load branches\" lists the repo's real branches in a dropdown; without it, you type the branch and it saves. Open a ticket in that project: its code anchors are links that open the file on GitHub at the chosen branch (with line anchors). Stakeholders see the repo + branch read-only as a link. A project using only the legacy local code_root still gets the on-disk anchor check from the linter."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: ADR-032 records the GitHub-repo-reference + read-only-token decision; new fields documented in the project schema JSON (repo_url, branch). Token-setup caveat noted in the summary.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T20:22:23Z
---

So we are crystal clear which branch and project we are working on we should implent this

## Conversation

**2026-06-05 20:22 claude:** Run run-20260605-2022 completed — A project's "Code repository" is now a GitHub repo URL + branch instead of a local folder path — so it's clear exactly which repo and branch a project (including the new system projects) is working on. On a project's Overview you paste the GitHub URL and set the branch; click "Load branches" to pick it from the live list, which needs a read-only GitHub token on the server (without one, you just type the branch — everything else still works). A ticket's code references (code anchors) now become clickable links that open the file on GitHub at that branch. The old local path still exists for any project that prefers an on-disk check, but GitHub is now the default way to point at code.

Heads-up for it to light up fully: a read-only GitHub token (fine-grained, Contents + Metadata read on the org repos) needs to be set as GITHUB_TOKEN on the server. Until then the branch is free-text. Want me to /schedule a reminder to wire that token, or will you set it?
