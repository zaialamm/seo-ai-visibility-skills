---
name: keyword-brief
description: "Turn a seed topic into a short keyword brief with real volumes, difficulty, intent and the site's current Google position. About $0.05–0.07 per brief."
---

# Keyword brief

## Goal

A one-screen brief the user can act on: the keywords worth a page, their real numbers, the questions to answer on that page, and where the site stands today.

## Required inputs

- A seed keyword or topic.
- Country and language (default `us` / `en`; two-letter codes).
- Optional: the user's domain, for rank checks.

## Before spending

- Call `citable_prices` (free) once per session.
- A brief costs about $0.005 (suggest) + $0.03 (metrics for up to 20 keywords) + $0.012 per rank check. Ask before more than 3 rank checks.
- Failed calls are never charged.

## Tools

- `citable_keyword_suggest(seed, country?, lang?, depth?, limit?, sources?)` — $0.005. Autocomplete expansions from Google, YouTube and Bing: `suggestions[]` and `questions[]`, each scored 0–1.
- `citable_keyword_metrics(keywords, country?, lang?)` — $0.03 for 1–20 keywords in one call: volume, CPC, competition, difficulty, intent. Unknown keywords come back with null metrics — report them as "no data", not zero.
- `citable_rank_check(keyword, domain, gl?, hl?, num?)` — $0.012. Google position of the domain plus the top 10.

## Workflow

1. `citable_keyword_suggest` with `depth: 2`. Pick up to 20 keywords: the seed, its strongest head terms, and 5–8 `questions[]` — questions become the page's headings later.
2. `citable_keyword_metrics` once, with all 20 in one comma-separated call. One batch costs the same as one keyword; never call it per keyword.
3. If a domain was given: `citable_rank_check` on the top 3 by volume × intent fit.
4. Output:
   - Table: keyword · volume · difficulty · intent · current position (or —).
   - The 3–5 questions worth answering on the page, verbatim from `questions[]`.
   - One recommendation: which keyword the page should target and why.
   - Total spent, from the `_payment` fields.

## Guardrails

- Do not pad the list with keywords you made up; every row must come from a tool result.
- Country and language must match the market the user sells in — ask if unclear.
- Rank checks are per keyword; do not loop them over the whole list without asking.
