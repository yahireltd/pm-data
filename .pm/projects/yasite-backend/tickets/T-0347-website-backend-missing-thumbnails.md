---
id: T-0347
title: website backend missing thumbnails
type: feature
state: review
priority: p2
created: 2026-06-10T11:08:11Z
updated: 2026-06-10T11:44:39Z
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
    status: completed
    summary: "Fixed the missing product thumbnails in the backend admin for the CHL (Chair Hire London) site. The backend was only ever looking for the newer \"webp\" images, but CHL's products are still almost all on the older jpg/png images, so most thumbnails came up blank. It now chooses images the same way each public site does: CHL shows the old image first and only uses a webp if that's all there is, while Yahire shows the webp first and falls back to the old image. We also fixed the thumbnails being cropped on the reorder-category screen so the whole product is now shown, centred, in each tile. Without this, staff reordering or managing CHL categories in the admin saw blank/cut-off images and couldn't tell products apart. The public websites are unaffected."
    progress:
      - at: 2026-06-10T11:22:19Z
        note: "Root cause: the backend admin app runs with newWebsite=1, so chl (websiteID 2) thumbnails were resolved webp-first while chl has almost no webp. The shared resolver YaImagesWebp::getThumbnailImage did old-jpg-first then webp for chl, which is the opposite of the desired order. Fix: for chl, check webp first and fall back to the old jpg/png images table when no webp exists. yahire (websiteID 1) is unchanged. Frontends are unaffected because they select the primary thumbnail via the productThumbnail/productThumbnailWebp relations and only hit this resolver when no primary image exists. Changed common/models/YaImagesWebp.php; php -l passes."
      - at: 2026-06-10T11:37:46Z
        note: "Corrected fix. Desired behaviour: yahire (websiteID 1) = webp first then old images; chl (websiteID 2) = old images first then webp. Two changes: (1) common/models/YaStockCategoriesAssignment.php getCategoryProducts now picks the primary thumbnail by websiteID instead of the global newWebsite param. The backend admin sets newWebsite=1, which was forcing webp-first for chl too; chl has almost no webp, so its thumbnails came up empty. Now chl uses the productThumbnail (old jpg/png) relation as primary in the backend exactly like the working chl public site. (2) common/models/YaImagesWebp::getThumbnailImage now resolves per website in both directions: chl old->webp, yahire webp->old (previously yahire had no old fallback). Frontends unchanged: chl frontend already used old-first and yahire frontend already used webp-first, which is what the websiteID rule produces. php -l clean on both files. Not yet committed/deployed."
    test_plan: |-
      Backend admin only (public sites should look identical to before):
      1. Site Management > Reorder Category Items > pick a CHL category (e.g. "Chair Hire"): every tile shows a thumbnail, fully visible and centred (not cropped top/bottom). Products with a new webp show the webp; the rest show the old jpg/png.
      2. Site Management > Items for a CHL category: thumbnails present (this screen resolves images via the same shared image lookup).
      3. Backend Search results for CHL products: thumbnails present.
      4. Switch to Yahire (websiteID 1) in the same screens: thumbnails unchanged from before.
      5. Spot-check both public frontends (CHL + Yahire) category pages: visually identical to before.
      Note: changes are in the working tree only — not yet committed or deployed. Hard-refresh the browser so the updated stylesheet loads when verifying the centring.
    ended: 2026-06-10T11:44:39Z
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-10T11:44:39Z
version: 7
---

On yasite backend the thumbnail are missing for chl, as only the webp is displayed, but chl doesn't have webp, just a few, so it should be the jpg/png first then fallback to webp or if webp not found use the jpg/png, yahire should use webp and fallback to jpg/png

## Conversation

**2026-06-10 11:44 claude:** Run run-20260610-1109 completed — Fixed the missing product thumbnails in the backend admin for the CHL (Chair Hire London) site. The backend was only ever looking for the newer "webp" images, but CHL's products are still almost all on the older jpg/png images, so most thumbnails came up blank. It now chooses images the same way each public site does: CHL shows the old image first and only uses a webp if that's all there is, while Yahire shows the webp first and falls back to the old image. We also fixed the thumbnails being cropped on the reorder-category screen so the whole product is now shown, centred, in each tile. Without this, staff reordering or managing CHL categories in the admin saw blank/cut-off images and couldn't tell products apart. The public websites are unaffected.
