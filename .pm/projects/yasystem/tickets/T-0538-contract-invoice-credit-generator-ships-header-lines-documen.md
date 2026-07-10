---
id: T-0538
title: Contract invoice/credit generator ships header≠lines documents (Xero rejections, wrong proformas) — adjuster dumps residuals, diff engine has 4 bugs
type: bug
state: triaged
created: 2026-07-10T16:20:57Z
updated: 2026-07-10T17:07:17Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: deep dive with Austin 2026-07-10
assignee: null
acceptance_criteria:
  - adjustInvoiceLineTotals only reconciles ≤ ~5p of rounding and keeps qty×unitPrice == netAmount; larger discrepancies refuse generation with a loud log naming contract + both engines' numbers
  - A saved invoice/credit note ALWAYS satisfies header total == |Σ qty×unitPrice| and header VAT == |Σ vatAmount| (violation blocked, logged, digest-visible)
  - "compareContractVersions: stale $item fixed, $xeroNominal reset per line, reversal VAT uses the OLD item's rate — covered by ContractPricingTest additions"
  - Census query returns zero NEW rows after deploy (existing 34 tracked for repair separately)
  - Root cause of hire-date-change zeroing priceFixed identified (fix here or split to follow-up ticket)
out_of_scope: []
code_anchors:
  - path: common/models/YaContracts.php
    note: "generateInvoiceWithItems ~4845 (two unreconciled engines: getBalances headers vs compareContractVersions lines); compareContractVersions ~10582 (stale $item ~10619, sticky $xeroNominal, reversal VAT rate); adjustInvoiceLineTotals ~10848 (unbounded residual dump onto netAmount/vatAmount, unitPrice untouched); createInvoiceLineItemsFromDifferences ~10883; splitLineItemsByBalance ~11043; flattenOtherLines ~10808"
  - path: common/models/XeroFunctionsNew.php
    note: posting payload reads quantity/unitPrice/vatAmount and IGNORES netAmount — why adjusted documents are Xero-rejected
  - path: common/tests/unit/pricing/ContractPricingTest.php
    note: existing test scaffolding for the diff engine
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - invoicing
  - xero
  - data-integrity
attention: null
version: 4
---

## Problem

34 documents since 2025-01 (census on the 02-Jul live snapshot) have header totals that disagree with their line items — credit notes/invoices that Xero permanently rejects ("The document total does not equal the sum of the lines", "Tax amount cannot be greater than the line amount" — live example: credit note 75664, erroring on every posting run since June), and **type-2 proformas that are customer-facing and simply wrong** (worst: proforma 68216 header £0.01 vs lines £4,890.24; 67004 header £120 vs lines £2,498).

## Root cause chain (traced via acceptedchanges + code)

`YaContracts::generateInvoiceWithItems` computes the document from **two independent engines that are never reconciled**:
- **Header totals** ← `getBalances()['uninvoicedBalanceLessVat'/'uninvoicedVatBalance']` (account arithmetic: contract total minus already-invoiced).
- **Line items** ← `compareContractVersions($lastInvoicedVersion, $currentVersion)` (version-diff arithmetic).

When they disagree, `adjustInvoiceLineTotals` (YaContracts ~10848) "fixes" it by **dumping the entire residual onto the last line's `netAmount`/`vatAmount` — without touching `unitPrice`/`quantity`, and without any size limit**. The Xero payload however is built from `quantity × unitPrice` + `vatAmount` (`XeroFunctionsNew::postItemisedInvoices/CreditNotes`) and ignores `netAmount`. So every adjusted document is internally contradictory: **Xero computes the line total from the UNadjusted unitPrice while carrying the ADJUSTED vatAmount** → both rejection messages fall out naturally.

**Worked example — credit note 75664 (contract 45435):** acceptedchanges 2026-06-03 20:35:14 shows the contract being zeroed (contractPrice 503.63 → **−0.02**, VAT 100.73 → −0) by user 687. Header from balances = credit £503.65/£100.73 ✓. Version diff found no item changes (only header prices were forced), emitting just the insurance/waiver other-line (−£14.67). The adjuster then dumped −£488.98 net + the full −£100.73 VAT onto that waiver line's netAmount/vatAmount. Result: stored line = qty 1 × unitPrice −14.67 with vatAmount −100.73 → Xero: tax (100.73) > line (14.67) AND doc total (503.65) ≠ Σ lines (14.67). Permanently unpostable. (Also note the zeroing flow left the contract at −0.02, not 0.)

**Worked example — credit note 78307 (C091771):** an amendment saga (06-23/24) in which **changing the hire end date zeroed `priceFixed` on multiple items** (acceptedchanges: "Fixed Price Changed 11→0", "17.49→0", "8.49→0", "9.75→0", "−180→0" at the exact moment of the hireEnd change; changing the date BACK did not restore them). The eventual credit (07-01) mixes increase lines, (Reversal) lines, a Compensation line, and a "Collection £60 with VAT −£60" line; header 29.52/5.90 vs lines +330.48/−5.90.

## Contributing bugs in `compareContractVersions` (YaContracts ~10582)

1. **Stale variable** (~10619): `$products[$item->stockID]` uses `$item` — a leftover from the PREVIOUS loop — instead of `$newItem`; wrong product's nominal fetched.
2. **Sticky `$xeroNominal`**: never reset per line; when a lookup misses, the previous line's nominal silently leaks in.
3. **Reversal VAT at the wrong rate**: old-item reversal lines use the NEW item's vatRate.
4. Package-parent skips + override fields make the item-diff systematically diverge from the balance arithmetic (the mixed-sign '-1:1'/'1:-1' split path has its own totals logic).

## Upstream data bug (separate but feeding this)

**Hire-end-date changes zero items' `priceFixed`** (and don't restore on revert) — visible verbatim in acceptedchanges for C091771. Every contract that has dates nudged loses its price fixes, changing contract totals and manufacturing version-diffs. Needs its own investigation/fix — likely responsible for a large share of the amendments that then hit the generator bugs.

## Proposed fix

1. **Stop the silent dump**: in `adjustInvoiceLineTotals`, only reconcile GENUINE rounding (≤ a few pence) and do it COHERENTLY (adjust unitPrice so qty×unitPrice == netAmount). Any larger discrepancy = a bug signal: refuse to generate, log loudly (contract, both engines' numbers), surface in the daily digest.
2. **Invariant at save**: after building any invoice/credit, assert header total == |Σ qty×unitPrice| and header VAT == |Σ vatAmount| (Xero's own validation, applied locally) — violations blocked + flagged, never shipped to customers or Xero.
3. Fix the four compareContractVersions bugs (stale $item, sticky nominal, reversal VAT rate, per-line nominal reset).
4. Separate ticket-worthy: the date-change → priceFixed-zeroing flow.
5. **Repair**: review/regenerate the 34 broken documents (list reproducible via the census query; several are live posting blockers, several are customer-facing proformas).

## Census query (zero API, re-runnable tripwire)

```sql
SELECT i.id, i.type, i.contractID, i.created, i.total, i.VAT,
       ROUND(SUM(ii.quantity*ii.unitPrice),2) lineSum, ROUND(SUM(ii.vatAmount),2) vatSum
FROM invoices i JOIN invoice_items ii ON ii.invoiceID=i.id
WHERE i.voided=0 GROUP BY i.id
HAVING ABS(ABS(i.total)-ABS(lineSum)) > 0.02 OR ABS(ABS(i.VAT)-ABS(vatSum)) > 0.02;
```
Candidate for inclusion in the unposted report / T-0534 digest as an ongoing tripwire.

## Conversation

**2026-07-10 16:32 claude-code:** **priceFixed-zeroing mechanism located (hypothesis refined with Austin — it is NOT date changes per se).**

Data: 608 contract date changes in 2026 vs only 134 'Fixed Price Changed → 0' events (~40 contracts) — so date edits do not systematically wipe fixes. The real pattern: fixes are zeroed when the ITEM ROWS are rewritten by a contract/quote-builder save — bursts like contract 48404 (8 items' fixes zeroed in one second during an amendment with no date/qty changes on those items) and 48521 (fix zeroed in the same second as a qty change). Date changes correlate (80/134) only because date edits go through the same save flow.

Sink: `YaContractItems::saveItemsAndAccessories` (line ~619) — `$contractItem->priceFixed = $item['priceFixed'];` overwrites verbatim with whatever the browser posts, for every row on every save. Callers: SalesController ~2567/~14811 (quote/contract builder screens — view-contract.php / quote-builder.php). So any builder interaction that rebuilds a row's client-side state without carrying the loaded fix posts 0 → fix silently wiped → contract totals shift → amendment diffs manufactured → generator bugs (this ticket) turn them into broken documents.

**Fix plan for this part:**
1. Server-side guard in saveItemsAndAccessories: `array_key_exists('priceFixed', $item)` distinguishes absent-vs-explicit; absent → PRESERVE stored value (explicit 0 stays legitimate — users can unfix).
2. Trace/fix the builder JS so loaded fixes always round-trip (2-minute repro: sandbox UI → open a contract with a fixed price → change a qty → save → inspect the POSTed items payload in devtools).
3. Keep the acceptedchanges 'Fixed Price Changed → 0' trail as the tripwire (it's how this was found).

**2026-07-10 16:50 claude-code:** **Zeroing mechanisms enumerated (with Austin) + scope decisions + test strategy.**

**How contracts get zeroed (all verified in data):**
1. **Issue-resolution compensation items** (Austin's find): 'Compensation for: Contract Cancelled', stockID 788, itemNo 8888-001-003, issueTypeOptionID=7, **status=100**, full contract value negated into quotedPriceUnit AND priceFixed. Writer: YaContractItems.php:822 (`priceFixed = -round($issueModel->chargeCompo,2)`). Examples: contracts 48207 (−927.85), 48687 (−207.00), and 45435 (−598.47 — credit note 75664's contract). **Created with NO acceptedchanges entry** — invisible in the change trail.
2. **100% discount items** (stockID 962, YaContracts ~11315-11360): only discounts vatable+non-fixed-price+non-charge+non-credit lines (`$isDiscountable`), so 100% ≠ zero on contracts with fixed/charge items; per-line rounding then pct-rounding produces the ±pennies residue (45435's −0.02). Also saved with save(false) — **bypasses the changes log too**.
3. Cancellation via unconvert (SalesController ~1086 'Cancelled by …unconvertReason') — flow located, not yet traced.

**Refined divergence hypothesis for cancelled contracts:** 45435's archive versions 3-5 ALL contain the compensation line, so it cancels out of the version-diff — yet the balances/price engine registered a −503.65 move on 2026-06-03. The three engines (recalculatePrice, getBalances, compareContractVersions) appear to apply **different filters to special items (status=100, compensation, discount)** — investigate each engine's status/type filtering as the concrete divergence point.

**Scope decisions (Austin):** proformas parked → **T-0539**; cross-project audit (yahirenew website invoice generation, chl sister site) → **T-0540**.

**Test strategy (Austin's design — adopt as this ticket's test plan):** REWIND-AND-REPLAY. For each broken document: reconstruct the contract state at generation time from ya_contracts_archive/ya_contract_items_archive (versions exist for all cases checked), re-run generation with the FIXED code in a dry-run harness (console command, no writes), and diff the would-be document against the actually-stored one (still on live/sandbox DB). Acceptance: (a) all 34 broken docs regenerate CORRECTLY (header==Σlines, amounts match manual expectations); (b) a control sample of healthy contracts regenerates IDENTICALLY to what was originally produced (no regression).

**2026-07-10 17:07 claude-code:** **Implementation plan AGREED with Austin (2026-07-10), with six binding constraints. NO LIVE ROLLOUT of any of this yet — everything below is build/test/sandbox only until explicitly cleared.**

1. **Silent-writer logging** (discount + compensation items → acceptedchanges): agreed, build as-is. Zero behaviour change.
2. **priceFixed preservation** — CONSTRAINT: the quote builder keeps its entire working state in SESSION (`$quoteNo.'.items'` etc., SalesController ~2343; save reads session, not the form; edits accumulate via AJAX handlers). Intended price changes travel through that session state, and the code is fragile (Austin: colleague-built, "sketchy"). Design: (a) audit every session `.items` writer to find which handler drops priceFixed; (b) fix at the writer(s); (c) server-side array_key_exists guard stays as backstop — but must be verified against the session flow so INTENDED price changes are never swallowed. Test: full builder matrix on the test system (qty change, date change, add/remove item, explicit price fix, explicit unfix) with payload/session inspection.
3. **Mechanical diff-engine fixes** (stale $item, sticky nominal, reversal VAT rate): agreed — each must be PROVEN by a failing-then-passing unit test in ContractPricingTest.
4. **Invariant** — CONSTRAINT: `Invoices` is saved from many unrelated flows (balance updates, Xero GUID write-backs, voiding, payments). The invariant must NOT be a model-level save hook. It attaches ONLY at document-generation call-sites (generateInvoiceWithItems and siblings), as an explicit check inside the existing generation transaction. Shadow mode first (log-only), enforce later and only after shadow data is clean.
5. **Adjuster** — CONSTRAINTS: (a) legitimate rounding adjustments must be arithmetically coherent AND visible — a labeled line ('Rounding adjustment'), never a silent mutation of an unrelated line; (b) refusal is NOT the goal: the objective is documents that GENERATE CORRECTLY (otherwise refusals just hand Austin the manual work back in a new form). Engine alignment (6) must make the known cases — cancellations via compensation items, discounts — generate right; refusal remains only for genuinely unknown divergence, and each refusal is a bug to fix, not a steady state.
6. **Engine filter-map investigation** (recalculatePrice vs getBalances vs compareContractVersions on status-100/compensation/discount items): agreed — investigation first, design from evidence.

**Test strategy (unchanged):** unit tests → rewind-and-replay harness on the test DB (34 broken must regenerate correct; healthy control set must regenerate IDENTICAL; compensation/discount corpus must pass invariant) → end-to-end on test system + demo company via the T-0534 posting rig (cancel via issue resolution → credit → post) → live only after all green and explicit sign-off, logging-first ordering.
