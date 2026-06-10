---
id: T-0346
title: breadcrumbs css
type: feature
state: in_progress
priority: p2
created: 2026-06-10T09:41:23Z
updated: 2026-06-10T09:55:52Z
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
  - path: frontend/views/yahire/product.php
    lines: 163-185
    symbol: product breadcrumbs ($this->params['breadcrumbs'])
  - path: frontend/views/yahire/category.php
    lines: 28-33
    symbol: category breadcrumbs ($this->params['breadcrumbs'])
  - path: frontend/web/css/yahire.css
    lines: 1136-1146
    symbol: .breadcrumb / .breadcrumb a
  - path: frontend/web/css/yahire.css
    lines: 239-243
    symbol: .divider img
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
labels: []
attention: null
version: 9
department: Sales
---

hey \
 \
Can we change font/size/style of  the product page breadcrumbs to be like the category breadcrumbs? 

![](https://yahire.zendesk.com/attachments/token/9qUNBeYQSq0gcu4FmRd81HKrH/?name=image.png)\
![](https://yahire.zendesk.com/attachments/token/G8H39dHFp3qakKTMYHis2I3Uw/?name=image.png)
