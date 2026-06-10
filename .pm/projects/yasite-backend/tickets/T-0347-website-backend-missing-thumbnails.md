---
id: T-0347
title: website backend missing thumbnails
type: feature
state: in_progress
priority: p2
created: 2026-06-10T11:08:11Z
updated: 2026-06-10T11:22:19Z
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
    test_plan: |-
      Backend admin only:
      1. Site Management > Items for chl (websiteID 2): products with a new webp show the webp; products with only the old jpg/png now show that old image instead of a missing/placeholder thumbnail.
      2. Backend search results for chl: same fallback behaviour.
      3. yahire (websiteID 1) backend thumbnails unchanged.
      4. Confirm both public frontends (chl + yahire) are visually unchanged.
labels: []
attention: null
version: 5
---

On yasite backend the thumbnail are missing for chl, as only the webp is displayed, but chl doesn't have webp, just a few, so it should be the jpg/png first then fallback to webp or if webp not found use the jpg/png, yahire should use webp and fallback to jpg/png
