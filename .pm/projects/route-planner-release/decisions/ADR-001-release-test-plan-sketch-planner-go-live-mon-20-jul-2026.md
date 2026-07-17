---
id: ADR-001
slug: release-test-plan-sketch-planner-go-live-mon-20-jul-2026
title: Release test plan — sketch planner go-live (Mon 20 Jul 2026)
state: accepted
decided: 2026-07-17
decided_by:
  - Austin Pickering
project: route-planner-release
supersedes: null
superseded_by: null
tickets:
  - T-0611
  - T-0612
  - T-0613
kind: test-plan
version: 3
---

# Context

The sketch planner + run-planner integration releases to live on Monday 20 Jul. The UAT meeting (M-001) returned a GO with follow-up tickets, all now built. The residual risks are silent loss (jobs/items missing off finalised runs), incorrect weights/volumes, concurrent-edit overwrites, same-day plan replacement, and stale plans (restored/old plans whose contracts changed) writing bad runs. Canonical detail: `backend/views/route-planner/RELEASE-PLAN-2026-07-20.md` (branch PickingSketchSalesDashFriday).

# Decision

Weekend testing on the sandbox combines automated conservation checks with a manual destruction checklist. The automated audit (`php yii planner-audit/date YYYY-MM-DD`, T-0612) runs **after every finalise** and must PASS (exit 0).

## Scenarios

**S1 Core workflow** — solve clean future date → finalise → runs on run planner → audit PASS; repeat re-solve+re-finalise twice (no losses); locked routes survive re-solve; run headers show real length/cost/max load.

**S2 Splits & items (top risk)** — logistics manual item split → load → green "as split by logistics" per piece → re-solve preserves split → finalise → run item lists match sketch → audit PASS. Solver split (amber provisional) finalises to identical lists. Half-luton solve → finalise recombines same-vehicle pieces → audit PASS. sketch-planner.log has one FINALIZE-ITEMS line per piece with correct source.

**S3 Same-day protection (T-0611)** — TODAY sketch: red dialog, confirm dead until typing TODAY; direct POST without confirm_today refused server-side and logged; future dates unchanged.

**S4 Concurrency (two people)** — second user blocked by name on same date; sketch↔run planner mutual block; 60s stale-session release; takeover banner; simultaneous saves on different dates don't bleed.

**S5 Destruction** — double-click finalise (one run set only); kill browser mid-finalise then audit + re-finalise; delete a run then audit flags the due-contract gap; loading run auto-locks through re-solve; midnight-boundary finalise.

**S6 Visibility** — failed-collection pins only for open failures; map routes on live-loaded plans; bold addresses; break banners (mid-route, trailing, return-leg, mid-drive memo).

**S7 Supersede / restore / staleness (T-0613)** — finalize plan A, then finalize a re-solved candidate B: A flips to `superseded` (finalize-supersede logged), purple restore banner appears. Restore A → working draft (old draft archived), delta banner flags contract changes. Staleness on finalize: a contract moved to another date / gone self-collection/self-return / cancelled → stop SKIPPED with named warning, audit PASS on the remainder; weight drift >10% / postcode change / window change / service-time increase → warning "planned X, now Y — consider a re-solve" but the stop finalises with CURRENT details. Restore + finalise of a TODAY plan still triggers the typed-TODAY dialog.

## Entry criteria
- Branch pulled on sandbox, hard-refreshed; solver box already live (byte-matched).
- Review tickets T-0600–T-0613 closed or consciously deferred.

## Exit criteria (gate to merge)
- All scenarios pass; every finalise audit exits 0; no unexplained sketch-planner.log WARN/SKIP entries.
- Remaining open decision resolved: split job-minutes policy (T-0606) — proportional vs full-per-piece vs floor.

## Sign-off
- Austin (developer/release owner) + one logistics tester (Orfield or Conor) confirm the checklist; sign-off recorded as an outcome on M-001 or a comment on this ADR, then this ADR moves to accepted.

# Consequences

Week 1 on live: daily `planner-audit/date` runs, sketch-planner.log tailing during finalises, drive-time feedback via the per-leg logging. Fallback is the unchanged run planner (no rollback needed for planner misbehaviour); last-resort rollback is reverting the merge commit — migrations are additive and stay.