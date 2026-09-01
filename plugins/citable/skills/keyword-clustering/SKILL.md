---
name: keyword-clustering
description: "Turn a keyword list into page groups with real volumes: one target page per cluster, a priority order, and a cannibalization check against the site's current rankings. About $0.03 per 20 keywords plus $0.02 per rank check."
---

# Keyword clustering

## Goal

A page plan, not a keyword dump: which keywords belong on the same page, which page each group deserves, in what order to build them, and where the site already ranks two of its own URLs for the same intent.

## Required inputs

- A keyword list (paste, file, or the output of `keyword-brief`) — or a seed to expand first.
- Country and language (default `us` / `en`; two-letter codes).
- Optional: the user's domain, for the cannibalization check.

## Before spending

- Call `citable_prices` (free) once per session.
- Metrics cost $0.03 per batch of up to 20 keywords — 100 keywords is $0.15. Say the total before the first batch.
- Rank checks are $0.012 each; ask before more than 5.
- Failed calls are never charged.

## Tools

- `citable_keyword_suggest(seed, country?, lang?, depth?, limit?)` — $0.005. Only if the user gave a seed instead of a list.
- `citable_keyword_metrics(keywords, country?, lang?)` — $0.03 per call of 1–20 keywords: volume, CPC, competition, difficulty, intent. Always fill a batch; never call per keyword.
- `citable_rank_check(keyword, domain, gl?, hl?, num?)` — $0.012. The domain's Google position and the URL that holds it.

## Workflow

1. Normalise the list: lowercase, trim, drop exact duplicates. If it came from a seed, keep `suggestions[]` with `matchesSeed: true` plus every `questions[]` item.
2. `citable_keyword_metrics` in batches of 20 until every keyword has numbers. Keywords with null metrics stay in the list marked "no data" — they still cluster.
3. Cluster by hand, from the data: same intent, shared head term, and the same answer would satisfy the searcher. Questions cluster under the expansion they ask about. A cluster is a page; if two keywords need different pages to rank, they are different clusters.
4. Name each cluster by its highest-volume keyword. Priority = cluster volume ÷ median difficulty, intent-weighted (commercial and transactional first for a product site, informational first for a content site — ask which).
5. If a domain was given: `citable_rank_check` on the head keyword of the top 5 clusters. Two clusters ranking with the **same** URL means that page is stretched across two intents; one cluster ranking with **two different** URLs of the domain means the site competes with itself. Both are cannibalization findings.
6. Output:
   - Table: cluster · target page (existing URL or "new") · keywords · total volume · median difficulty · intent · current position.
   - Cannibalization findings, each with the two URLs and the one to keep.
   - Build order: top 5 clusters with a one-line reason each.
   - Total spent, from the `_payment` fields.

## Guardrails

- Every keyword in a cluster must come from the user's list or a tool result — never add keywords to fill a cluster.
- Do not merge clusters to make the list shorter; a cluster is defined by the page it needs.
- Volumes are estimates from a licensed clickstream source; say so when the user treats them as exact.
