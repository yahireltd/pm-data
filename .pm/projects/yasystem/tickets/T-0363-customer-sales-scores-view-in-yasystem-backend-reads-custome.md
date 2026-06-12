---
id: T-0363
title: Customer sales-scores view in yasystem backend (reads customer_sales_scores table from T-0360 scoring)
type: feature
state: review
created: 2026-06-12T02:53:11Z
updated: 2026-06-12T02:59:48Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "A backend page lists customer_sales_scores: domain, company, business, score, tier, evidence, spend at scoring, scored date"
  - Sortable by score/spend/date and filterable by tier and domain text
  - Read-only — no write paths to the scores table from the UI
  - Works on the test box once customer_sales_scores.sql has been applied
out_of_scope:
  - The scoring pipeline itself (T-0360)
  - Stamping scores onto ya_customers (optional SQL provided separately)
  - Nav menu integration (page reachable by URL; menu link is a one-line human edit)
code_anchors:
  - path: backend/controllers/SalesScoresController.php
    symbol: new read-only controller (to be created)
  - path: common/models/CustomerSalesScores.php
    symbol: new ActiveRecord model (to be created)
  - path: backend/views/sales-scores/index.php
    symbol: new GridView page (to be created)
  - path: backend/controllers/DepositChargesController.php
    symbol: idiom reference for backend CRUD controllers
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0253
    model: claude-fable-5
    started: 2026-06-12T02:53:58Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-12T02:53:58Z
    ended: 2026-06-12T02:58:25Z
    summary: |-
      **What we did:** Built a read-only "Customer Sales Scores" page inside the yasystem backend showing the AI sales-potential scores from the customer scoring work — each customer email domain with its company, what the business is, the 0–100 score, colour-coded tier (A green / B amber / C grey), the evidence sentence, their spend at scoring time, and the scoring date. The page is searchable by domain or company name, filterable by tier (with live counts), and sortable by score, spend or date. It's already deployed to the test server with all 329 scored customers loaded (136 A / 117 B / 76 C), so it can be clicked and reviewed right now.

      **Why:** The scoring data lived only in a SQL file and ticket comments — invisible to anyone working in yasystem where sales and accounts actually look at customers.

      **If we did nothing:** The scores would stay a one-off analysis artefact. Nobody would see that, for example, a Tier-A global events giant only spends a fraction of its potential with us, and the lead list would never reach the people who can act on it.

      **The benefit:** The sales team gets a self-serve, always-current view of which customers have untapped events potential (sort by score, compare against spend) — and as the scoring runs continue in tranches, new rows appear automatically because the page reads the same table the scoring pipeline loads into.
    test_plan: |-
      All on the TEST box (Routing Test server — data and files already deployed there):
      1. Log into the yasystem backend on the test box and open /index.php?r=sales-scores (or /sales-scores if pretty URLs are on). The page should render "Customer Sales Scores" with a grid of 329 rows, default-sorted by score descending (clarionevents.com and hilton.com at 100 on top).
      2. Filter by Tier A — count shown in the dropdown should be 136; the grid should only show green score badges.
      3. Type "hilton" in the search box — should match hilton.com, hiltonbankside.co.uk and canopylondoncity.com (matches domain OR company).
      4. Click the Spend At Scoring column header — sorts by spend; check ft.com (£456k) tops it.
      5. Click any domain — opens the customer's website in a new tab.
      6. Edge cases: Reset button clears filters; page 2+ pagination works (50 per page); a tier filter combined with a search term works together.
      7. Cross-impact: none expected — three NEW files only (common/models/CustomerSalesScores.php, backend/controllers/SalesScoresController.php, backend/views/sales-scores/index.php); no existing files or DB tables were modified, no write paths to data.
      8. To take this live: (a) run ~/Desktop/customer_sales_scores.sql against the live DB, (b) commit the three files to master — NOTE the local checkout is currently on the refund-hardening branch; the new files are untracked so they'll follow you when you `git checkout master`. Per project policy the commit/push is yours to make. Optional: add a nav menu link (one-line human edit).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - sales-scoring
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-12T02:58:25Z
version: 6
---

## Problem

T-0360 produced AI sales-potential scores for customers (329 domains so far, growing in tranches; data loaded via ~/Desktop/customer_sales_scores.sql into the `customer_sales_scores` table). There is no way to see these scores inside yasystem where the sales/accounts team works.

## Plan

Read-only Yii2 backend page (SalesScoresController + GridView) over `customer_sales_scores`, matching the existing backend CRUD idiom: tier filter, domain search, sortable score/spend/date columns, colour-coded tiers. New files only (model + controller + view) so nothing collides with the in-flight refund-hardening branch; commit to master is a human step per project agent policy.

## Conversation

**2026-06-12 02:58 claude-code:** Run run-20260612-0253 completed — **What we did:** Built a read-only "Customer Sales Scores" page inside the yasystem backend showing the AI sales-potential scores from the customer scoring work — each customer email domain with its company, what the business is, the 0–100 score, colour-coded tier (A green / B amber / C grey), the evidence sentence, their spend at scoring time, and the scoring date. The page is searchable by domain or company name, filterable by tier (with live counts), and sortable by score, spend or date. It's already deployed to the test server with all 329 scored customers loaded (136 A / 117 B / 76 C), so it can be clicked and reviewed right now.

**Why:** The scoring data lived only in a SQL file and ticket comments — invisible to anyone working in yasystem where sales and accounts actually look at customers.

**If we did nothing:** The scores would stay a one-off analysis artefact. Nobody would see that, for example, a Tier-A global events giant only spends a fraction of its potential with us, and the lead list would never reach the people who can act on it.

**The benefit:** The sales team gets a self-serve, always-current view of which customers have untapped events potential (sort by score, compare against spend) — and as the scoring runs continue in tranches, new rows appear automatically because the page reads the same table the scoring pipeline loads into.

**2026-06-12 02:59 claude-code:** **Branch deviation record (retro, per T-0364):** the claim resolved branch=master, but the shared local checkout of Ya-Hire-Management was on `refund-hardening-t0331-stripe-dryrun` (another in-flight stream). I did not switch the shared checkout. The three new files exist as UNTRACKED files in that working tree (they follow any checkout) and are deployed to the test box; they are destined for **master** — Austin commits/pushes per the locked agent policy. Protocol gap for this situation is tracked as T-0364 on pm-tool-self.
