---
name: competitor-citations
description: "Analyze one competitor through what AI answer engines already cite them for: the prompts they win, the pages doing the work, and their share of AI citations against your domain. About $0.65 per competitor, all from an index of real AI answers."
---

# Competitor citations

## Goal

Turn a competitor's AI visibility into a plan: which buyer questions AI answers with their pages, what those pages look like, and how far behind (or ahead) the user's domain is — then the five prompts worth going after first.

## Required inputs

- One competitor domain (`example.com`, no scheme).
- Optional: the user's domain, for the share-of-voice comparison.
- Optional: one buyer question the user wants checked live. Only if they supply it.

## Before spending

- Call `citable_prices` (free) once per session.
- A full run is $0.18 (share of voice) + $0.25 (cited prompts) + $0.22 (top cited pages) = $0.65. Add $0.05 per engine for a live `ai-visibility` check the user asks for. State the total first.
- Failed calls are never charged.

## Tools

- `citable_ai_share_of_voice(domains, platform?)` — $0.18 for 2–10 domains in one call: mentions, AI search volume, and each domain's share of the compared set.
- `citable_cited_prompts(domain, limit?, platform?)` — $0.25. The questions the index has seen AI cite this domain for: question, answer snippet, AI search volume, the exact URL cited, and how often it was observed.
- `citable_top_cited_pages(domain, limit?)` — $0.22. The domain's most-cited pages with per-engine and per-language splits.
- `citable_ai_visibility(prompt, domain, engines?)` — $0.05 per engine. A live run across the engines for **a prompt the user supplied**.
- `citable_rank_check(keyword, domain)` — $0.012. Optional: Google position for a prompt that reads like a search query.

## Workflow

1. `citable_ai_share_of_voice` with the competitor and the user's domain (and any other rivals they name, up to 10). This sizes the gap in one call: if the user's share is 0 %, say so plainly before spending more.
2. `citable_cited_prompts` on the competitor, `limit: 50`. Sort by `aiSearchVolume`; drop homonym noise (questions clearly about a different "brand" with the same name — flag them, do not silently delete). These are observed prompts; treat them as the demand map.
3. `citable_top_cited_pages` on the competitor, `limit: 10`. Match pages to prompts by `citedUrl`. Note the page *format* AI keeps citing — comparison page, glossary entry, pricing page, how-to — because format is what the user can copy; the content is not.
4. Only if the user supplied a buyer question: `citable_ai_visibility` for the user's domain on that question to confirm the live state. Never write the prompt for them.
5. Output:
   - Share of voice: user vs competitor, mentions and share, with the vendor's note that share is of the compared set.
   - The 5 prompts to go after: question · AI volume · competitor's cited URL · what that page's format is.
   - Page patterns: 2–3 formats the competitor wins with, each tied to the prompts it wins.
   - One next action: the single page to build or rewrite first, and which prompt it targets.
   - Total spent, from the `_payment` fields.

## Guardrails

- Never generate buyer prompts. The only prompts in the report come from `cited_prompts` (observed) or from the user.
- ChatGPT rows in the index are US/English only; say so when the market is elsewhere.
- Do not present a competitor's cited page content as something to reproduce — the takeaway is format and question coverage.
