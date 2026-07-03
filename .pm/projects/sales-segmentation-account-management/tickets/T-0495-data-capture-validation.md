---
id: T-0495
title: Data Capture / Validation
type: feature
state: triaged
priority: p2
created: 2026-06-30T14:26:11Z
updated: 2026-07-03T02:08:52Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-013
children: []
order: 16384
reporter: null
assignee: null
acceptance_criteria:
  - "The AI Score/Segment/suggested-Level is treated as a SUGGESTION: the UI lets a human ADJUST score & segment with a mandatory reason, and every override is logged to a correction log that can feed re-scoring ('computer learns')."
  - "A QUALIFY step captures the per-level qualifying answers + the missing firmographic data (the ~95% gap: companyType, decision-maker, budget, event calendar) at the point of taking ownership — enrichment-on-demand, not a bulk back-fill."
  - "Defines WHO validates and WHEN: steward (System) / exec (Incubation) / AM (Account) / senior AM (Strategic), at ownership hand-off; passing qualify advances Suggested → Qualified."
  - "Closes the loop with the T-0474 review-queue: 'proposed:<phrase>' no-fit segments and human corrections are reviewed and fed back."
  - Validation actions never silently overwrite — original AI suggestion is retained alongside the human-confirmed value for audit + model feedback.
out_of_scope: []
code_anchors:
  - path: docs/p0018-sales-segmentation/P-0018-phase2-process-flow.mmd
    role: the VALIDATE → QUALIFY steps
  - path: common/models/CustomerSalesScores.php
    role: where score/segment + a correction/override lives
  - path: docs/p0018-sales-segmentation/P-0018-account-level-design.md
    role: enrichment-on-demand at the Incubation gate (section 4)
relates:
  - T-0496
  - T-0497
  - T-0474
  - T-0457
  - T-0486
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 11
---

## Problem
The score & segment are AI suggestions and ~95% of accounts lack firmographics. We need a way to (a) validate/correct
the suggestion, (b) capture the data we don't have, and (c) feed corrections back — with clear ownership of who does
it and when.

## Design notes
Ben's chain, made concrete: **AI suggests → Human adjusts → Human qualifies.**
- **Adjust** — inline confirm/override of score & segment with a reason; write to a correction log (retain the AI
  original). This both fixes the record and trains the next re-score.
- **Qualify** — at ownership hand-off, the owner answers the per-level qualifying questions (T-0496) and captures the
  missing data; passing advances **Qualified** and drives T-0497's 'in the bag' %.
- **Who/when** — steward / exec / AM / senior AM at hand-off, per level.
- Ties to T-0474's closed-vocab review queue (no-fit → 'proposed:<phrase>' → human review).

## Relates
T-0496 (questions/data per level), T-0497 (progress), T-0474 (vocab + review queue), T-0457 (engine).

## Conversation

**2026-06-30 17:31 claude-code:** **Qualify gate built (MVP) — the "in the bag %" is now real & clickable** (sandbox, branch p0018-sales-segmentation-design, uncommitted). Pairs with T-0496 (per-cohort) + T-0497 (tracker).

- **Per-level checklists** — `common/components/LevelRequirements.php`: stacked qualifying questions + data-capture per level (Incubation 7 → Account 12 → Strategic 16), BANT-per-level + the design's data gates. Sensible defaults, **[WORKSHOP]-tunable** (→ T-0479 later). Mandatory items flagged.
- **Human progress stored separately** — table `account_level_qualify` (customerID PK, target_level, qualify_json, in_bag_pct, status). Kept apart from `customer_account_levels` so the nightly recompute rebuilds the *suggestion* without wiping a person's work.
- **UI** — `/account-levels/view?id=` shows the account + the checklist; ticking items / filling fields and saving (`/account-levels/qualify` POST) computes **in_bag % = items done**, sets **status=Qualified** when all mandatory are done, and surfaces in the worklist's "In the bag" column + a `work →` link.

Validated end-to-end (Alexandra Palace: 5/12 → 42%, owner_assigned until mandatory complete). RBAC: grant `/account-levels/*` for non-superadmins. Next: the **adjust/override** half of validation (correction log feeding re-score), and wiring the real questions with Ben.

**2026-06-30 18:00 claude-code:** **2026-06-30 — Adjust/override half built (MVP)** — the other half of this ticket (the *qualify* half shipped 17:31). The AI's score/segment/level is now a suggestion a human can change, with a reason, and every change is logged. Sandbox, branch `p0018-sales-segmentation-design`, **uncommitted** (project policy).

**What a user can now do** (on `/account-levels/view?id=`):
- **Override the bucket** — move an account to a different Stewardship Level (System/Incubation/Account/Strategic) with a reason. Applies immediately and the nightly recompute will *not* wipe it.
- **Correct the AI segment/score** — fix company type, industry, classification, score or tier, with a reason. These feed the next re-score so the computer learns.
- See the engine's **plain-English "why this level"** next to the suggestion, so the decision to split an account into a different bucket is informed (not blind).
- **Revert** any override back to the engine suggestion; full **correction history** is shown per account.
- The worklist marks human-overridden accounts (✎) with a count + an "Overridden" filter, and the qualify checklist now runs against the *human-decided* level, not the raw suggestion.

**How it meets the acceptance criteria:**
- *Suggestion, not truth; logged for re-scoring* → new durable table `customer_account_corrections` (append-only; one active row per field per account; keyed by canonical customerID per T-0486). Console `account-level/corrections 1` exports the JSON re-score feed and stamps rows as fed.
- *Never silently overwrite — AI original retained* → each correction stores `suggested_value` (the AI value) alongside `corrected_value` + reason + who/when.
- *Closes the loop / computer learns* → console `account-level/apply-corrections` overlays human segment/score truth onto `customer_sales_scores`, so the next `recompute` respects it; the feed export is the artifact handed to the next scoring run.
- *Survives recompute* → corrections live in a separate table (same pattern as the qualify table); level overrides apply at read-time via `COALESCE(override, confirmed, suggested)`.

**Architecture note:** the engine table is rebuilt (truncate + insert) on every recompute, so human decisions deliberately live *outside* it — recompute can never clobber a person's work.

**Tested end-to-end on sandbox** (then cleaned up): seeded a level override (Incubation→Account) + a score correction (96→95) on barbican.org.uk → `apply-corrections` overlaid the score, the worklist's effective level resolved to Account, the feed exported + marked fed, revert restored the suggestion. Files: `console/migrations/m260630_190000_*`, `console/controllers/AccountLevelController.php` (apply-corrections, corrections), `backend/controllers/AccountLevelsController.php` (override/revert), `backend/views/account-levels/{view,index}.php`.

**Still open (the "finer details" of splitting accounts):** the qualifying-question content per level + the £ thresholds that drive the suggested split are still sensible defaults — they need Ben's workshop input (and a percentile study → T-0479). RBAC: grant `/account-levels/*` for non-superadmins.

**2026-06-30 18:52 claude-code:** **2026-06-30 — follow-up: correction dropdowns now use the scoring taxonomy** (sandbox, uncommitted; see TS-004).

On the override panel, Industry and Company type were free-text. Changed them to **dropdown + type-to-autocomplete** sourced live from the scoring table (`customer_sales_scores`) — the same canonical values scoring assigned (25 industries, 62 company types), frequency-ordered so the common ones surface first. Corrections now converge on canonical values instead of inventing variants. A genuinely new value is still allowed (a correction is sometimes "we don't have this type yet") and will surface for the T-0474 review queue to fold in. When T-0474 builds proper vocab tables, the helper just points at those — one-line change (comment left in code).

**2026-07-02 12:14 claude-code:** **2026-07-02 — Code committed + pushed** in commit **`cede0ffb`** (branch `p0018-sales-segmentation-design`, pushed to origin; a direct commit outside the usual run workflow, allow_commit was off).

This ticket's share: both halves of validation — the **override loop** (`customer_account_corrections` migration; durable append-only log keeping the AI value beside the human value; override/revert/history UI + taxonomy-backed dropdowns in `AccountLevelsController` + `view.php`; console `account-level/apply-corrections` + corrections feed) and the **qualify gate** (`common/components/LevelRequirements.php` + `account_level_qualify` migration).

**2026-07-03 00:26 claude-code:** Verification pass (2026-07-03) against the committed code in cede0ffb7 — this ticket is PARTIAL, not done. What is actually built vs outstanding:

BUILT (committed, both halves of the validation loop):
- Adjust/override: customer_account_corrections table (console/migrations/m260630_190000, append-only; suggested_value kept beside corrected_value); override + segment/score correction UI with taxonomy dropdowns, revert, and full history (backend/controllers/AccountLevelsController.php:133-186, backend/views/account-levels/view.php:64-150); level overrides survive recompute via read-layer COALESCE (AccountLevelsController.php:37, :217-221); re-score feed export + fed_to_model stamping (console/controllers/AccountLevelController.php:181-202) and apply-corrections overlay onto customer_sales_scores (:155-172). AC5 (never silently overwrite) is fully met.
- Qualify gate: per-level stacked checklists incl. the firmographic gap items — decision-maker, need, event calendar, budget (common/components/LevelRequirements.php:16-39); account_level_qualify table (m260630_180000); qualify POST computing in-bag % and advancing status='qualified' when all mandatory done (AccountLevelsController.php:100-126); worklist 'In the bag' column + Overridden filter.

OUTSTANDING (the ACs as written are not yet satisfied):
1. Reason is optional, AC says mandatory — AccountLevelsController.php:159 accepts blank reason (?: null); neither reason input in view.php has required. Small fix: reject override/correction POSTs with empty reason (server-side) + required attr.
2. No ownership hand-off exists — the qualify step is not tied to "point of taking ownership" because owner assignment (runbook step 6: logged changeAccountManager wrapper, owner accepts, split_ownership_flag) is not built. Qualify is reachable by anyone, anytime.
3. WHO/WHEN is docs-only — roles per level live in P-0018-phase2-operating-runbook.md:13-17/:25-31 with [WORKSHOP] markers; nothing in code encodes or enforces steward/exec/AM/senior-AM validation rights.
4. T-0474 loop cannot close — corrections feed export exists and dropdowns tolerate new values for a future review queue, but T-0474 (vocab tables, review queue, 'proposed:<phrase>') is unbuilt; zero 'proposed:' handling in code. Blocked on T-0474, not on this ticket.

Cross-impact note for the eventual reviewer: apply-corrections mutates customer_sales_scores rows in place (original AI value survives only in the corrections log), so any other consumer of that table (scoring exports, segment-profile, fast-track) will see human-corrected values after the console command runs.

Also: migrations committed but run state per environment unverified; /account-levels/* needs an RBAC grant for non-superadmins; checklist content + £ thresholds remain sensible defaults pending the workshop (→ T-0479).

**2026-07-03 02:08 claude-code:** Gap #2 from the reconciliation ("no ownership hand-off exists") is now closed (2026-07-03, Austin-approved): the work page has an **Owner card** — shows the home row's real account manager (ya_customers.salesID), assign/change via a dropdown of the 19 active salespeople (+ Unmanaged to clear), writing through the existing YaCustomers::changeAccountManager() and logging `owner_assign` in customer_account_level_log. Home row ONLY — no cascade to same-domain rows; a ⚠ split-ownership warning lists other AMs on sibling rows instead (decision 15 stays with the workshop). The stage machine is now honest end-to-end: **Owner assigned lights only when a real salesID exists**, and **Confirm requires an owner AND Qualified** — so the qualify step is finally tied to "point of taking ownership" as this ticket's AC demanded.

Baseline data for the workshop: 355 of 20,044 home accounts already carry a real owner; only **1 domain in the entire base has split ownership** — decision 15's conflict case is nearly empty in practice, so the cascade rule can be settled cheaply. Files: backend/controllers/AccountLevelsController.php (assign-owner action + ownershipFor + confirm guard), backend/views/account-levels/view.php (owner card + stage derivation). Uncommitted on the branch pending Austin's next commit.
