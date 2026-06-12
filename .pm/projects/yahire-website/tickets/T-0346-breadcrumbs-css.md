---
id: T-0346
title: breadcrumbs css
type: feature
state: done
priority: p2
created: 2026-06-10T09:41:23Z
updated: 2026-06-12T00:11:31Z
project: yahire-website
section: null
parent: null
children: []
order: 1024
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Breadcrumbs on product view to look the same as the category page"
out_of_scope: []
code_anchors:
  - path: yahirenew/web/css/origin.css
    lines: 4253-4289
    symbol: .product-detail__found-in* (product breadcrumb CSS — edited)
  - path: yahirenew/views/yahire/product.php
    lines: 209-222
    symbol: product breadcrumb markup (.product-detail__found-in)
  - path: yahirenew/views/yahire/category.php
    lines: 64-89
    symbol: .category-breadcrumbs CSS (reference target style)
  - path: yahirenew/views/yahire/category.php
    lines: 338-343
    symbol: category Breadcrumbs widget (class=breadcrumb category-breadcrumbs)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260610-0955
    model: claude-opus-4-8
    started: 2026-06-10T09:55:52Z
    status: in_progress
    progress:
      - at: 2026-06-10T10:01:53Z
        note: "Identified root cause: live app is `yahirenew`, not `frontend`. Product page uses a bespoke breadcrumb block (.product-detail__found-in, label \"Can be found in\", 18px/600 weight, 13px links, big flex gaps, no separator) while the category page uses the Yii Breadcrumbs widget styled via .category-breadcrumbs (14px, normal weight, \" / \" separators, prefix \"You are here:\"). Made a CSS-only change in yahirenew/web/css/origin.css (.product-detail__found-in*) so the product breadcrumb matches the category typography: label 18px/600 -> 14px/400, links 13px/500 -> 14px/400, removed flex gaps, added \" / \" separators after the label and between links. Kept link color #243638 (matches category's dark link). OPEN QUESTION: left the product prefix wording as \"Can be found in\" rather than changing it to the category's \"You are here:\" — the ticket asked for font/size/style only, so wording is a separate copy decision for the reporter."
      - at: 2026-06-10T10:02:45Z
        note: 'Per reporter, also changed the product breadcrumb prefix wording from "Can be found in" to "You are here: " in yahirenew/views/yahire/product.php:211 to fully match the category page. Now both CSS (origin.css) and label text match the category breadcrumb. Two files changed in working tree; commit/push still pending on the human (project policy locks agent commit/push).'
      - at: 2026-06-10T10:08:01Z
        note: "Review feedback addressed: product breadcrumb stopped at the category and didn't show the current product. Added the product name as the final (non-link) crumb in yahirenew/views/yahire/product.php (`<span class=\"product-detail__found-in-current\">` with Html::encode($product->productName)), mirroring how the category page's last crumb is the current page name. Updated origin.css: added .product-detail__found-in-current (same dark #243638, weight 400) and generalised the separator to `.product-detail__found-in-links > * + *:before { content \" / \" }` so \" / \" renders before every crumb including the product. Breadcrumb now reads: \"You are here: / Chair Hire / [Subcategory] / Product Name\"."
      - at: 2026-06-10T10:23:03Z
        note: "Review feedback (weight mismatch) resolved. Root cause: the category breadcrumb sets NO font-weight in any stylesheet, so it inherits the body base weight which is `font-weight: 500` (Pangea Medium) at origin.css:437 — that's the 'bold' look. My previous round had over-corrected the product crumbs to font-weight 400 (Pangea Regular), making them lighter than the category. Fixed by setting .product-detail__found-in-label, .product-detail__found-in-link and .product-detail__found-in-current all to font-weight 500 to match the inherited body/category weight. Product breadcrumb now matches category on weight (500), size (14px), separators (' / ') and color (#243638). Note: category breadcrumb is display:none below 768px; product breadcrumb stays visible on mobile (left as-is, since there's nothing to compare against there)."
      - at: 2026-06-10T10:29:02Z
        note: "Recreated the ya-hire source-tier docs system on yasite (it had no docs/ folder). Added docs/README.md describing the MVC-mirrored convention, adapted for yasite's multiple web tiers (frontend/yahirenew/backend/console) with a tier prefix, and noting yahirenew is the live public site. Documented this change at docs/views/yahirenew/yahire/product.md — covers the bespoke .product-detail__found-in breadcrumb, the T-0346 fix, and the key gotcha (category 'bold' is just inherited body weight 500 / Pangea Medium, so product crumbs must be 500 not 400 to match). New files: docs/README.md, docs/views/yahirenew/yahire/product.md."
    test_plan: |-
      1. On desktop (>=768px) open a product page and its category page side by side.
      2. Confirm the product breadcrumb text now has the same weight as the category breadcrumb (Pangea Medium / 500) — no longer visibly lighter.
      3. Confirm size (14px), the ' / ' separators and dark #243638 color still match.
      4. Confirm the category/subcategory crumbs are still links (teal hover) and the product name is plain text.
      Files changed: yahirenew/web/css/origin.css (font-weight 400 -> 500 on the three .product-detail__found-in* rules) and yahirenew/views/yahire/product.php (product-name crumb + wording, from earlier rounds).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: created the docs folder and the sub folders same as on ya-hire
labels: []
attention: null
version: 26
department: Sales
surface: yahire-website
---

hey \
 \
Can we change font/size/style of  the product page breadcrumbs to be like the category breadcrumbs? 

![](https://yahire.zendesk.com/attachments/token/9qUNBeYQSq0gcu4FmRd81HKrH/?name=image.png)\
![](https://yahire.zendesk.com/attachments/token/G8H39dHFp3qakKTMYHis2I3Uw/?name=image.png)

## Conversation

**2026-06-10 10:27 — you**

the breadcrumbs on the product view are changed to look the same as on the category pages
