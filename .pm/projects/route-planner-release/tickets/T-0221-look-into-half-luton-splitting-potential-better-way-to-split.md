---
id: T-0221
title: look into half luton splitting - potential better way to split if they get re-combined anyway on finalise.
type: feature
state: done
priority: p2
created: 2026-06-04T14:04:19Z
updated: 2026-07-23T19:33:32Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
reporter: null
assignee: null
acceptance_criteria:
  - "[x] add a half luton option in the split options on the sketch planner that makes it through to the solver"
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
branch: PickingSketchSalesDashFriday
version: 12
---

## Problem

_Suggested feature from meeting M-003._

look into half luton splitting - potential better way to split if they get re-combined anyway on finalise.

## Conversation

**2026-07-16 17:30 claude-code:** **Decision from the M-001 UAT meeting (16 Jul, via Rob):** enable half-luton splitting as a selectable option in the sketch planner's Split dropdown — the DEFAULT stays full_luton. This is in scope for Monday's release. So the remaining work here is: (1) confirm the solver/split pipeline handles split_strategy=half_luton end-to-end (the splitter itself has the size table; check finalise recombination behaves), (2) add "Half Luton" to the Split dropdown on sketch + run planner solve controls, default unchanged.

**2026-07-16 17:42 claude-code:** **Built (commit b45ab018, branch PickingSketchSalesDashFriday):** "Half Luton" is now in the sketch Split dropdown; default remains Full Luton per Rob's request. No backend change needed — the splitter has supported half_luton chunk sizes (570kg / 48m³) since the original build, only the dropdown was missing the option. Finalise behaviour unchanged: same-vehicle adjacent pieces still merge (the ticket's original "re-combined anyway" observation), so half-luton splitting just gives the solver finer packing freedom without creating extra run rows. **To verify:** solve a busy day with Split=Half Luton — expect more/smaller pieces on the board, same or better drop count, and normal-looking runs after finalise; then confirm Full Luton is still the default on a fresh page load.

---

**2026-07-23 19:33 — you**

Done

---

**2026-07-23 19:33 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
