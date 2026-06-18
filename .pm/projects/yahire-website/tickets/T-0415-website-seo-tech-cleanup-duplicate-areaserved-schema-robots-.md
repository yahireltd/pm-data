---
id: T-0415
title: Website SEO/tech cleanup — duplicate areaServed schema, robots.txt, www/.php redirects, junk URLs
type: bug
state: triaged
created: 2026-06-18T07:39:39Z
updated: 2026-06-18T07:40:31Z
project: yahire-website
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Thanos Mamo
  channel: email
  contact: athanasios@yahire.com
assignee: null
acceptance_criteria:
  - Homepage, category, and product pages each output a single areaServed in their schema (no duplicates), confirmed in a schema validator / Rich Results Test.
  - robots.txt is populated (not empty), reviewed against the old site's robots.txt, with intended allow/disallow directives and a sitemap reference present.
  - www.yahire.com 301-redirects to the non-www canonical host across all pages.
  - Legacy .php URLs 301-redirect to their clean-URL equivalents.
  - /index and /index.php no longer resolve as live pages (301 to the homepage or 404).
  - /wedding-furniture-hire/0 and similar trailing-/0 paths no longer resolve (redirect to the correct category or 404).
  - All redirects are 301 (permanent) and resolve in a single hop to one canonical URL (no chains/loops).
  - A crawl (e.g. Screaming Frog) confirms no junk/duplicate URLs from this list remain reachable for the same content.
  - Redirect rules and ported robots.txt content match the agreed map from the open question (confirm intended www/.php rules + whether an old redirect file exists to port).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - seo
  - website
attention: null
version: 2
---

## Problem

Several technical-SEO issues on yahire.com raised by Marketing (thread: "Schema Index canonicals etc").

## Open items

1. **Duplicate `areaServed` in schema** on homepage / category / product pages — dedupe.
2. **robots.txt looks empty** — check the old site's robots.txt and port anything that should carry over.
3. **Redirects missing / not working** — `www → non-www`, and legacy `.php` → clean URLs. As of 20 May, `/index` and `/index.php` still resolve.
4. **Junk URLs that shouldn't exist (should redirect or 404):**
   - `https://www.yahire.com/wedding-furniture-hire/0`
   - `https://www.yahire.com/index.php`
   - `https://www.yahire.com/index`

## Already resolved (no action)

- Product description missing from schema — **done** ("description is there now", 20 May; Austin noted it's an optional tag, no harm).

## Open question

- The redirect side was left unclear in the thread ("unsure what happened on the redir side"), and Zsolt asked for specifics (3 Jun). Confirm the intended redirect rules (full `www→non-www` + the `.php` map) and whether the old site had a redirect file to port.