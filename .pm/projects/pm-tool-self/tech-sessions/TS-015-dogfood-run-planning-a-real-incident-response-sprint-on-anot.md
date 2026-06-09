---
id: TS-015
slug: dogfood-run-planning-a-real-incident-response-sprint-on-anot
title: "Dogfood run: planning a real incident-response sprint on another project (yasystem)"
project: pm-tool-self
created: 2026-06-09T19:34:31Z
updated: 2026-06-09T19:35:03Z
decisions:
  - "Finding 1 (blocker): creating the FIRST sprint in any project fails with a file-system error, because the write path takes its lock file before anything has created the sprint folder. This blocked the core ask of the run — the yasystem sprint could not be created at all. Each failed attempt also consumed a sprint number (SPR-001 and SPR-002 are now burned on yasystem). Filed as the bug ticket linked below; workaround is creating the folder on the server by hand."
  - "Finding 2 (convention gap): the agent only followed the working rules (claim before code, Definition of Ready, plain-language write-ups) because the pm-tool code repository happened to be on the same machine and it could read AGENTS.md from disk. An agent connecting from anywhere else would use the tools blind to the conventions — the server-side gates would stop bad writes, but without ever telling the agent the rules up front. The owner spotted this live during the run."
  - "What worked well: eight fully-specified tickets (acceptance criteria, code anchors, out-of-scope, priorities, effort, backlog status) were created on yasystem without friction; the project's locked agent policy (no commit/push to the live branch) was surfaced clearly by the project lookup before any work; and concurrent writing held up — a human filed an inbox ticket mid-run and the ID allocator gave out interleaved ticket numbers with no collision or duplicate. The agent initially mistook that interleaved number for a burned ID — worth remembering that ticket numbers are global across all projects and the inbox, so gaps in one project's sequence are normal."
actions:
  - text: Fix the lock-before-folder-creation ordering in the shared write path (and check the CLI/web mirrors), then decide whether burned sprint numbers are acceptable or allocation should move after a successful write.
    ticket: T-0329
  - text: Serve the working rules through the MCP itself (server instructions, a resource, or a digest in the project lookup agents already call first), from a single source of truth so the served copy cannot drift from the repo copy.
    ticket: T-0330
  - text: "Once the first-sprint bug is fixed or the folder workaround is applied on the server, finish the yasystem run: create the Refund Process Hardening sprint and commit its seven ready tickets (T-0320, T-0321, T-0323 to T-0327)."
side_quests: []
problem: "First real-world test of using the live MCP from a different project's workspace: an agent investigated a production refund incident on the Yahire ops system and was asked to turn the findings into a planned sprint with ready tickets on the yasystem project. We wanted to see whether the tool and its working conventions hold up when the work is NOT about pm-tool itself and the agent is not sitting in the pm-tool repo."
success_criterion: A planned sprint with Definition-of-Ready tickets exists on yasystem, and every piece of friction the run surfaced is captured as a ticket on this project rather than lost in a chat log.
phase: build
version: 4
---

# TS-015: Dogfood run: planning a real incident-response sprint on another project (yasystem)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
