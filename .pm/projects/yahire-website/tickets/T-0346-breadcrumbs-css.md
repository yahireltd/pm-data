---
id: T-0346
title: breadcrumbs css
type: feature
state: review
priority: p2
created: 2026-06-10T09:41:23Z
updated: 2026-06-10T10:08:05Z
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
  - Breadcrumbs on product view to look the same as the category page
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
    test_plan: |-
      1. Open a product page that belongs to a category (e.g. the product under "Chair Hire" from the review note).
      2. Confirm the breadcrumb now ends with the product name as plain (non-link) text: "You are here: / Chair Hire / <Product Name>" (and includes the subcategory when present).
      3. Confirm styling still matches the category page: 14px, normal weight, " / " separators, dark #243638 text, teal hover/underline on the clickable category/subcategory crumbs only (product name is not a link).
      4. Check a product with a subcategory and one without, to confirm separators render correctly in both cases.
      Files changed: yahirenew/views/yahire/product.php (markup) and yahirenew/web/css/origin.css (styles).
labels: []
attention:
  needed_by: human
  reason: "Added the missing product name as the final breadcrumb crumb on the product page (per review note). Breadcrumb now reads \"You are here: / Chair Hire / <Product Name>\" and matches the category page styling. Changes are in the working tree (yahirenew/views/yahire/product.php + yahirenew/web/css/origin.css) and not yet committed — let me know if you'd like me to commit/push now that the policy allows it."
  since: 2026-06-10T10:08:05Z
version: 17
department: Sales
---

hey \
 \
Can we change font/size/style of  the product page breadcrumbs to be like the category breadcrumbs? 

![](https://yahire.zendesk.com/attachments/token/9qUNBeYQSq0gcu4FmRd81HKrH/?name=image.png)\
![](https://yahire.zendesk.com/attachments/token/G8H39dHFp3qakKTMYHis2I3Uw/?name=image.png)
