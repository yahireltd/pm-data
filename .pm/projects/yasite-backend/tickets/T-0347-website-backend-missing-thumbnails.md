---
id: T-0347
title: website backend missing thumbnails
type: feature
state: in_progress
priority: p2
created: 2026-06-10T11:08:11Z
updated: 2026-06-10T11:37:46Z
project: yasite-backend
section: null
parent: null
children: []
order: 1024
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260610-1109
    model: claude
    started: 2026-06-10T11:09:05Z
    status: in_progress
    summary: Claimed via web UI
    progress:
      - at: 2026-06-10T11:22:19Z
        note: "Root cause: the backend admin app runs with newWebsite=1, so chl (websiteID 2) thumbnails were resolved webp-first while chl has almost no webp. The shared resolver YaImagesWebp::getThumbnailImage did old-jpg-first then webp for chl, which is the opposite of the desired order. Fix: for chl, check webp first and fall back to the old jpg/png images table when no webp exists. yahire (websiteID 1) is unchanged. Frontends are unaffected because they select the primary thumbnail via the productThumbnail/productThumbnailWebp relations and only hit this resolver when no primary image exists. Changed common/models/YaImagesWebp.php; php -l passes."
      - at: 2026-06-10T11:37:46Z
        note: "Corrected fix. Desired behaviour: yahire (websiteID 1) = webp first then old images; chl (websiteID 2) = old images first then webp. Two changes: (1) common/models/YaStockCategoriesAssignment.php getCategoryProducts now picks the primary thumbnail by websiteID instead of the global newWebsite param. The backend admin sets newWebsite=1, which was forcing webp-first for chl too; chl has almost no webp, so its thumbnails came up empty. Now chl uses the productThumbnail (old jpg/png) relation as primary in the backend exactly like the working chl public site. (2) common/models/YaImagesWebp::getThumbnailImage now resolves per website in both directions: chl old->webp, yahire webp->old (previously yahire had no old fallback). Frontends unchanged: chl frontend already used old-first and yahire frontend already used webp-first, which is what the websiteID rule produces. php -l clean on both files. Not yet committed/deployed."
    test_plan: "Backend admin, Site Management > Reorder Category Items, select a chl category (websiteID 2): items now show the old jpg/png thumbnail (and the new webp for the few products that have one). Repeat for Items and Search backend views (they call YaImagesWebp::getThumbnailImage directly). yahire (websiteID 1) backend unchanged. Confirm both public frontends are visually identical to before."
labels: []
attention: null
version: 6
---

On yasite backend the thumbnail are missing for chl, as only the webp is displayed, but chl doesn't have webp, just a few, so it should be the jpg/png first then fallback to webp or if webp not found use the jpg/png, yahire should use webp and fallback to jpg/png
