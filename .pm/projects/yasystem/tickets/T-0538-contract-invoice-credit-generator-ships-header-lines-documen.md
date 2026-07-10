---
id: T-0538
title: Contract invoice/credit generator ships header≠lines documents (Xero rejections, wrong proformas) — adjuster dumps residuals, diff engine has 4 bugs
type: bug
state: in_progress
created: 2026-07-10T16:20:57Z
updated: 2026-07-10T19:56:15Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260710-1721
    model: claude-opus-4-8
    started: 2026-07-10T17:21:01Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-10T17:21:01Z
    progress:
      - at: 2026-07-10T17:22:38Z
        note: "Session-writer audit complete for the two prime suspects — both priceFixed wipe mechanisms located precisely. (1) actionCalculateHireDays (SalesController 6100-6111): when the discount matrix returns no discounted price, the fix survives ONLY if both old and new hire durations are below the long-term-hire threshold ($paramsHireDays); crossing the threshold blanks priceFixed='' (line 6110) for every such item — and reverting the date does NOT restore (already blanked in session). This is C091771's exact story. Line 6094: when the matrix DOES return a discounted price it overwrites the fix with the matrix price. (2) actionUpdateItemQty (line 3825): unconditionally writes the recomputed price into priceFixed — manual fixes replaced by matrix/rate-card values on every qty change. KEY INSIGHT: priceFixed is semantically overloaded — it stores BOTH salesperson manual overrides AND computed prices (qty-discount matrix, long-term-hire prices are written into the same field). Blanket 'preserve' would therefore be WRONG: automatic recalcs legitimately rewrite computed values. The bug is that MANUAL fixes are indistinguishable and get clobbered/blanked by automatic recalcs. Fix needs a manual-vs-computed distinction (flag/column set by actionManualPriceUpdate, honoured by recalc flows) — design decision needed from Austin on the business rule. OPEN QUESTION to verify next: session items appear keyed by stockID (actionEditQuote line 2189 $items[$itemStockID]) — if true, contracts with duplicate same-stock lines (45435 has two 'Trestle Table (6ft)' rows) may collapse on hydration → potential silent line loss on edit; needs verification against the hydration loop and saveItemsAndAccessories' handling of absent existing items."
      - at: 2026-07-10T17:26:47Z
        note: "NEW FINDING — duplicate-stock line deletion hazard, confirmed end-to-end in code: builder hydration keys ordinary items by BARE stockID (SalesController 2161-2168; only special stockIDs 784/786-789 get a composite stockID_itemRef key), so two lines of the same product collapse to one in the session; saveItemsAndAccessories then DELETES any DB row whose id is absent from the session (YaContractItems 587-589, $removedFromSession -> delete()). Net effect: editing a contract that carries duplicate same-product lines silently deletes one line. Blast radius TODAY: census shows only 1 contract with duplicate live ordinary lines (likely 45435's two Trestle rows), 0 active — BUT the mechanism destroys its own evidence (each edit deletes the duplicate), so historical damage is unmeasurable; treat current-low-exposure with suspicion. There is prior history here: code comments reference docs/bugs/quote-builder-duplicate-accessory-orphans.md (accessory flavour of the same identity problem). Fix direction: composite key (stockID_itemRef) for ALL items, not just specials — but the session key format is used throughout the builder JS/AJAX, so this lands inside the careful builder work with the full test matrix, not as a quick patch. Priority within ticket: below the priceFixed manual/computed distinction (that one is actively mispricing contracts)."
      - at: 2026-07-10T17:35:17Z
        note: "Data sampling (Austin's suggestion) — three design-shaping facts. (1) Amendment generation is HOT PATH: 2026 H1 alone has 191 docs with 'Increased by' lines, 127 with reversal pairs, 235 with compensation lines — hundreds of amendment documents per half-year, so generator changes carry real volume. (2) Golden convention confirmed from healthy doc 72989: vatAmount is LINE-level VAT (not unit), unitPrice×qty = line net, and healthy documents satisfy the header==Σlines invariant EXACTLY (net −538.84 vs credit 538.84, VAT −107.77 vs 107.77) — strong evidence the invariant will not false-positive on healthy flows. 'Rounding' never appears as a description — the label is free for our coherent rounding line. (3) Fix population: 37,715 live items carry priceFixed — 18.8k ABOVE quoted price (long-term-hire computed totals), 18.3k below (qty-matrix/manual discounts), only 620 equal. Computed fixes overwhelmingly dominate; manual fixes are buried among them, confirming the manual/computed split as the core design need. discountmatrix/stock_discountmatrix tables exist → retro-classification (fix == matrix/LTH-computed value ⇒ computed; else manual) is feasible and would both size the manual population and seed the backfill for the new manual flag."
      - at: 2026-07-10T18:10:51Z
        note: |-
          Build phase 1 complete on branch t0538-document-generator-integrity (commits b6b5c6b8, 7f3bfd1a, 9189ef81). All pushed; test box (3.11.164.105) on branch, full pricing suite green: 38 tests / 138 assertions.

          SHIPPED:
          1. compareContractVersions mechanical fixes (all 3 loops): int-cast VAT codes with SAFE fallback per Austin — only explicit code 2 gives 0% VAT, anything unexpected defaults to 20% (never fall back to 0 VAT); reversal lines compute VAT at the OLD item's rate; $xeroNominal reset per line in main + removed-items loops; stale $products[$item->stockID] lookup fixed. Same safe default in getFullContractAsLineItems.
          2. Silent writers now log to acceptedchanges via new logChangeSafe (never throws — item already saved, logging must not abort flow): issue charge/compensation items ('Issue Charge Item Added'/'Issue Compensation Item Added'), discount item create/update/delete in syncDiscountItem ('Discount Item Added/Updated/Removed').
          3. Adjuster rework: adjustInvoiceLineTotals no longer smears residual into last line's netAmount (Xero renders qty×unitPrice → local vs Xero divergence = the 34 broken docs). Correction now on own coherent labeled line: 'Rounding adjustment' ≤ cap (2p/line, 5p floor) or 'Balance adjustment' + error log beyond. Old code also DROPPED the VAT residual when last line was zero-rated — fixed.
          4. Shadow invariant in generateInvoiceWithItems: log-only header-vs-rendered-lines check to 'invoice-integrity' channel.
          5. Engine census (live data): no null/odd vatRate codes; priceFixed NULL-or-numeric (engines agree post-roundtrip); old-accounting %-discount amendments extinct; found 28 no-VAT (contract vatRate=2) contracts historically amended with 20% VAT lines → FIXED: line engine + flattenOtherLines now honour contract-level vatRate (new lines use new version's, reversals/removals use old version's). Also found 6 positive-priced credit items (788/789) = data errors, engines disagree 2× — separate data fix needed.

          PROOF: tests run against pre-fix code (checkout 902b04cb YaContracts.php) = 5 failures + 2 errors; fixed code = all green. New tests call the REAL compareContractVersions (existing ContractPricingTest only tests a simulate copy).

          REMAINING: session-writer fixes (blocked on 2 business rules from Austin: manual fix vs LTH crossing; manual fix vs matrix price on qty change), rewind-and-replay harness on the 34 broken docs, quote-builder regression tests, duplicate-stock keying, 6 bad credit items data fix.
      - at: 2026-07-10T18:23:46Z
        note: |-
          MAJOR FINDING via replay harness: the issue-resolution charge-reversal flow creates 788/789 credit items with the SAME itemRef as the 786/787 charge they cancel (e.g. C082716: stock 786 +30 and stock 788 −30 both ref a5a6aa57). compareContractVersions keyed versions by bare itemRef → the credit collided with the charge, engine saw +30→−30 as a price change → emitted reversal −30 PLUS re-invoice −30 = DOUBLE-SIZED CREDIT NOTE. Systematic for every post-invoice charge reversal — this is very likely the cause of the "credit note twice as big" class of accounts complaints. Fixed with composite keys (itemRef|stockID + occurrence counter) in commit 0d9f70b7; unit test encodes live doc 52590.

          Replay harness numbers (doc-replay/run, read-only, all diff docs since 2025-07-01): 4208 docs replayed, 3946 healthy+stable under the new engine, 236 broken-before → 223 healed by the fixes (the stored-0.00 class = old adjuster smeared residuals into netAmount with unitPrice 0, which Xero renders as nothing). 6 apparent regressions all explained: 5 = the ref-collision double-count (now fixed, re-validating), 1 = doc 72858 C089368: package parent (stock 1005 Party Starter Pack) with NO children carrying its own price — both engines skip package parents so its £92 vanishes; archived insurance field also looks stale there (5.73 vs 97.73). Needs separate investigation.

          Also: 6 positive-priced credit items found (data errors): 5 from 2024 (finished contracts), 1 live-era = C091771 item 251316 'Compensation for: Late' at +180 (typed negative → double-negated). Sign guard added to createCreditNoteItem; the +180 row needs a live data fix (Austin). Unit suite: 39 tests / 142 assertions green on the box.
      - at: 2026-07-10T18:31:47Z
        note: |-
          Austin reversed the session-writer decision: quote-builder behaviour stays UNCHANGED (does not want to alter his colleague's quote-builder functionality without a dedicated review). No SalesController changes were made — verified clean; the t0538 branch contains only document-generation fixes (6 commits, b6b5c6b8..0d9f70b7). Created T-0541 (spike) to review manual-price survival properly: documents both wipe sites with line numbers, the initially-chosen-then-deferred rules (survive + UI flag in both cases), and the priceSource provenance design (NULL = legacy = current behaviour). T-0541 requires quote-builder regression tests before any implementation.

          Final replay validation with composite keys: 4208 docs — 3948 healthy-stable, 224/236 broken healed, 4 remaining regressions all explained (3 go-live-week docs likely manually corrected in DB at the time; 1 = doc 72858 package-parent-without-children + stale archived insurance, open investigation). 39 unit tests / 142 assertions green.
      - at: 2026-07-10T18:46:53Z
        note: |-
          CORRECTION to earlier replay figures + new tooling shipped (branch at 405626fd, deployed to test box).

          CORRECTED VALIDATION NUMBERS: earlier 'brokenBefore 236 / healed 224' was legacy-doc pollution — legacy-accounting contracts (created before go-live 2025-06-01) invoice via the legacy flow which stores NO itemised lines, so header-only is CORRECT for them, and the replay trivially 'matched' their headers. After scoping both tools to usesNewAccounting() contracts: 3,954 itemised difference docs replayed → 3,946 exactly stable under the fixed engine (99.8%), 4 broken-before (all data/upstream causes, not engine arithmetic), 4 explained exceptions (52385/52672/52400 = go-live week, likely Austin's manual DB corrections at the time; 72858 = package-parent-without-children + stale archived insurance, open).

          THE REAL LIVE DAMAGE LIST IS 4 DOCS ON 3 CONTRACTS: 78307/C091771 (the +£180 compensation data error), 75664/C089510 (header 503.65 vs lines −14.67), 70029+70030/C089676 (INV+CN pair, headers 3,412.29 vs lines ±99.38). All manual review/data fixes for Austin, all triageable on the new page.

          NEW TOOLING (per Austin's request): /doc-integrity (backend, read-only) — mismatch triage list with manual-refunds + Xero links and classification (no-lines/smeared/engine-mismatch), ?docs= pinning; /doc-integrity/view?id=N — stored lines with per-line coherence highlighting, live replay of what the FIXED engine would generate from the same archived version pair (adjuster preview, split-pair aware), raw version item diff, acceptedchanges trail (72h window, usernames), all docs on contract. Index embeds the expandable T-0538 change document: what/why per fix, testing evidence, Austin's manual work list, safety precautions, and temporary-vs-keep (nothing temporary committed; the two read-only tools are keep-recommended). Smoke-tested data paths on the box for docs 52590/68949/57069/78307.
      - at: 2026-07-10T19:05:43Z
        note: |-
          Austin triaged the 4-doc mismatch list using the new /doc-integrity pages + PDF links (PDF icons also added to manual-refunds/manual-payments statement rows via StatementTableHelper; fixed the dormant create-itemised-pdf endpoint which checked file_exists on the web path — the PDF had been generating fine).

          Dispositions (live xeroInvoiceID checked by Austin):
          - C089510 (INV 69663 + CR 75664): TEST CONTRACT by Zsolt (quote-builder author; customer 'Zsolt Turu', zsolt@yahire.com), cancelled via zeroing 2026-06-03 by Jhuztine. INV 69663 IS in live Xero (GUID dcc8f76d-9ef5-4883-87d9-61c56cd2c697) — Austin checking if already voided there; if not, void in Xero. CR 75664 never posted — its PDF is a textbook smear specimen: single line qty 1 × £−14.67 with netAmount £−503.65, summary claims £604.38. Plan: void both locally.
          - CR 78307 (C091771): never posted. Paula (sales) entered compensation 'her own way' → the +£180 double-negation. Needs Austin's data fix then regenerate/void decision.
          - INV 70029 + CR 70030 (C089676): never posted. Headers £3,412.29 vs lines ±£99.38 — accounts review needed.
          Safety note: all three unposted docs fail totals-vs-lines and get flagged to /xero/repair-docs rather than posting silently, but they will resurface every posting run until dispositioned — disposition before re-enabling posting.
      - at: 2026-07-10T19:15:31Z
        note: |-
          TRIAGE COMPLETE — final verdict on every flagged document. Customer check on the 4 mismatch contracts: C089510 = Zsolt test (zsolt@yahire.com), C089676 AND C089368 = Austin's bundle/test contracts (austin@yahire.com), C091771 = the only real customer (ARC / David Akinluyi).

          Bottom line: after legacy scoping and test-contract identification, EXACTLY ONE real customer document in the live era is broken: CR 78307 (Paula's +£180 negative-compensation double-negation, code-guarded going forward, awaiting Austin's data fix). Everything else on the mismatch list is internal test debris to void.

          This also CLOSES the last open replay exception: doc 72858 (C089368, package-parent-without-children + stale archived insurance) is Austin's test contract — mechanism documented as T-0541 evidence (quote builder writes package data in inconsistent states: same product priced-as-parent on C089510/C089676 vs isPackage-with-no-children on C089368), no accounts impact. The 3 go-live-week exceptions (52385/52672/52400) remain attributed to Austin's manual DB corrections at the time.

          Dispositions: void 69663 (in live Xero, Awaiting Payment — Austin has the link) + 75664, 70029, 70030 locally; fix item 251316 (+180 → −180) then re-examine 78307. T-0542 sharpened: all three polluting contracts used @yahire.com emails — internal-email exclusion would have caught every one.
labels:
  - invoicing
  - xero
  - data-integrity
attention: null
version: 22
branch: t0538-document-generator-integrity
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

**2026-07-10 17:29 claude-code:** **Builder regression strategy (Austin's requirement: prove builder changes don't break the builder). Four tiers, cheapest-per-confidence first:**

1. **Model-level unit tests for saveItemsAndAccessories** (AllocationCapTest pattern): update-vs-create matching, the absent-id deletion rule (incl. duplicate-stock case), new priceFixed semantics (manual preserved / computed rewritten / explicit unfix works). Mandatory.
2. **Session-scenario tests for the 7 session-writer actions** — the session is the builder's real interface (every AJAX action = f(session, POST) → session'): seed session, invoke action, assert resulting session. Scenarios: qty change on manually-fixed item; date change crossing the long-term-hire threshold (the 6110 blanking branch); manual fix; unfix; add; remove; discount apply/remove. Mandatory.
3. **Golden-master capture**: temporary sandbox-only instrumentation logging each builder action's session-in/POST/session-out while Austin clicks through the builder normally for ~10 minutes; recordings become replayable fixtures — after changes, replay all captured interactions and diff. Captures the colleague-built code's ACTUAL contract without reverse-engineering it. Mandatory before shipping builder changes.
4. **Thin Playwright smoke pack** (~6-8 flows against the sandbox builder UI, ~1 day to build): only tier that catches JS/DOM contract breakage. MANDATORY if the session key format changes (duplicate-line fix); optional otherwise.

Combined with the rewind-and-replay document harness, the full T-0538 proof stack: unit → session-scenario → golden-master replay → (conditional) browser smoke → document replay (34 broken correct + healthy identical) → e2e cancel→credit→post on demo org.

**2026-07-10 17:31 claude-code:** **Test plan addition (Austin): post-change invoicing assertions — edit → generate → assert exact lines.**

New tier between the builder tests and the document replay: scenario tests that run the REAL pipeline (contract state → archive version → builder-equivalent edit → generateInvoiceWithItems) and assert the exact invoice_items produced (description, qty, unitPrice, vatAmount, nominal) AND the header invariant. Core scenarios: qty increase ('Increased by N' line at right price/VAT); price change (reversal + re-invoice pair, VAT at the item's own rate); item removal (negative line); manual fix + date change past LTH threshold (fix preserved, only date-driven delta invoiced); cancellation via issue-resolution compensation (credit CONTAINS the compensation line — the 75664 case generating correctly); 100% discount (visible discount line, £0.00 residual not −£0.02).

**Correspondence oracle:** once the silent writers are logged (fix #1), assert two-way correspondence between acceptedchanges and the generated document — every line explainable by a logged change, every logged price-affecting change reflected in a line. The change log becomes the machine-checkable definition of 'intended changes', which is exactly how Austin verifies these by hand today.

**2026-07-10 17:33 claude-code:** **Scenario refinement (Austin): qty changes can cross discount-matrix price breaks — the whole line reprices, not just the delta.** Scenario matrix updated: (a) qty increase WITHIN a band → single '(Increased by N)' delta line at unchanged price; (b) qty increase CROSSING a break → reversal (−old qty × old price) + re-invoice (new qty × new price); (c) qty DECREASE crossing back DOWN a break → reversal + re-invoice at the HIGHER unit price (fewer units = more per unit — commercially sensitive, explicit test); (d) qty change where matrix returns nothing → delta line and no priceFixed blanking (the 6110 branch, post-fix).

**OPEN BUSINESS RULES needing Austin's decision before the priceFixed build:**
1. Manual fix vs long-term-hire threshold crossing (date changes): does a manual fix survive? (proposed: yes + UI flag 'manual price — review?')
2. Manual fix vs discount-matrix price on qty change: which wins when the matrix price differs from the manual fix (esp. when matrix is now CHEAPER)? Options: manual always wins / lower wins / manual wins + UI flag.

**2026-07-10 19:26 claude-code:** **Consolidated update — everything on branch `t0538-document-generator-integrity` (b6b5c6b8..2ecd2d40, test box current)**

**Engine fixes shipped:** (1) VAT codes int-cast, only explicit zero-rated code gives 0% — anything unexpected defaults to 20%, never 0; (2) reversal/removed lines use the OLD item's/version's VAT; (3) contract-level no-VAT honoured by amendment lines, charge diffs and first-invoice charges; (4) xeroNominal no longer leaks between lines; (5) **composite item keys** — the charge-reversal flow reuses the charge's itemRef for its 788 canceller, which made the diff engine emit reversal+re-invoice = double-sized credit notes (live doc 52590); (6) adjuster rework — residuals now on their own labeled 'Rounding adjustment'/'Balance adjustment' line instead of smeared into the last line's netAmount (which Xero ignores — the root of local-vs-Xero divergence); (7) log-only shadow invariant on every generated doc; (8) compensation/discount writers now log to acceptedchanges + compensation sign-guarded (-abs).

**Validation:** 39 unit tests / 142 assertions against the REAL engine, proven failing-then-passing vs pre-fix code. Replay harness (read-only, new `doc-replay/run` command) re-ran the fixed engine over every itemised difference doc since go-live: **3,946 of 3,954 byte-stable, 4 broken (all data causes), 4 explained exceptions**.

**Triage complete:** of the 4 live broken docs, 3 are internal TEST contracts (C089510 Zsolt; C089676 + C089368 Austin — all @yahire.com). **Only ONE real customer doc is broken in the entire live era: CR 78307** (C091771, Paula's compensation entered negative → double-negated to +£180). INV 69663 (test contract) leaked into live Xero as a £604.36 receivable — Austin voiding. Doc 72858 exception closed as test debris (package-inconsistency mechanism documented on T-0541).

**Tooling shipped:** `/doc-integrity` triage page (mismatch list, classification, manual-refunds + Xero links, expandable change document incl. manual work list + temporary-vs-keep) and `/doc-integrity/view?id=N` deep dive (stored lines w/ smear highlighting, live replay of what the fixed engine would generate, version item diff, acceptedchanges trail). PDF links throughout + PDF icons on manual-refunds/manual-payments statement rows. Fixed the dormant `create-itemised-pdf` endpoint (checked file_exists on the web path — PDFs had been generating fine all along).

**Tickets raised:** T-0541 (quote-builder review: manual-price survival rules + package-pricing evidence), T-0542 (stop test contracts posting to Xero — all three polluters used @yahire.com emails).

**In progress now — charges/compensation deep-dive (Austin's request):**
- **UK VAT gap confirmed structurally:** charges pick vatable/non-vatable stock (786/787) by issue type, but compensation ALWAYS uses vatable 788 — compensating a zero-rated supply reclaims output VAT that was never charged (incorrect per reg-38: adjustments must follow the original supply's rate). Fix direction: inherit VAT from the compensated item (chargeCompoStockID is already passed in).
- `actionGenerateCredit` hardcodes `round(amount/1.2)` to strip VAT — wrong on no-VAT/zero-rated contexts and a penny-rounding generator.
- Next: quantify occurrences in live data, then an end-to-end charge→compensate→reverse-charge→generate test on the box (the penny-rounding hotspot per Austin).

**Austin's manual list:** void 69663 in Xero + 69663/75664/70029/70030 locally; item 251316 +£180 → −£180 then re-check 78307; decide T-0534 release order; T-0541/T-0542 decisions.

**2026-07-10 19:44 claude-code:** **Charges/compensation flow: UK VAT verdict, guards shipped, and full E2E green (commits a58d4cad + a9342c78)**

**UK VAT question answered:** crediting 20% VAT against a zero-rated supply is NOT correct (reg-38: an adjustment carries the original supply's rate) — but the data shows **zero historical mispairs since go-live**: staff manually pick the 0% stocks (787/789 — 751 zero-rated credits, £122k), and the cancel-charge flow was already VAT-symmetric (786→788, 787→789). So no accounts damage exists; the fix is forward-only prevention, stamped writer-side at item creation, which means **no already-posted document recalculates differently** (Austin's concern — the rate lives on the item row and every engine reads the stored value).

**Guards shipped:** (1) createCreditNoteItem/createIssueChargeItem auto-swap to the 0% stock when the targeted product (chargeCompoStockID) or contract is zero-rated; (2) actionGenerateCredit strips VAT at the contract's rate instead of hardcoded /1.2; (3) actionCancelChargeCompo reversals now log to acceptedchanges (it was a third silent writer — it also never recalculated the contract price after reversing, noted: generation self-corrects); (4) **phantom-item trapdoor closed** — cloning an item can explicitly INSERT contractType NULL, and findAllIncSales filters on contractType, making the clone a real-in-SQL but invisible-to-everything row (excluded from recalculatePrice AND archiveNow). Hasn't fired in production (clones are DB-loaded) but both clone branches now set it explicitly.

**End-to-end harness green — 24/24** (`php yii charge-compo-e2e/run <contractID>`, sandbox-only, drives the REAL writers + generator): charge; compensation with odd pennies; **penny-rounding stress** (3×33p charges forcing per-line-VAT vs header sum-then-round onto the labeled 'Rounding adjustment' line — the exact mechanism behind the historic smears Austin flagged); cancel-charge in production shape (RV ref) AND the historic same-itemRef collision shape — **single correctly-sized reversal, no double-count**; zero-rated target compensation auto-selecting 789/0%. Every doc coherent (header == Σ qty×unitPrice), every action trailed. Evidence docs INV/CN #78399–78406 on sandbox contract C087538 for eyeballing via /doc-integrity.

**2026-07-10 19:56 claude-code:** **Correction from Austin + validations page (commit 2e497aa4)**

Austin corrected the VAT-selection understanding: the vatable/zero-rated split for charges and compensation follows the **type of charge**, not the product involved — a charge for a damaged/missing item is compensation for loss (0% in the UK, hence the issue-type mapping to stock 787/789), while a dirty charge is a cleaning **service** (20% even on a zero-rated product). My earlier target-product inference in resolveVatVariant would have wrongly zero-rated a cleaning charge on a zero-rated product, so the guard is **narrowed to contract-level no-VAT only** (the one safe inference); the issue-type mapping remains the business rule. E2E re-run: 24/24 green, now including an assertion that a zero-rated target does NOT change the caller's stock choice.

**New `/doc-integrity/validations` page** (Austin's request): the green counterpart of the mismatch list — every labeled 'Rounding adjustment'/'Balance adjustment' line ever written (only the fixed engine produces these; six already visible from the E2E runs, all sub-2p VAT reconciliations — the penny class), plus every document generated in a window with PASS/FAIL verdicts and investigate/PDF links, for side-by-side comparison against the same contract on live (where pre-fix documents smear or mismatch instead).
