---
name: competitive-landscape
description: "Map a market's search landscape across 2–5 competing domains: organic footprint, the keywords each domain wins, where their links come from, and where the openings are. About $0.07–0.17 per domain from a ranking index — no logins, no dashboards."
---

# Competitive landscape

## Goal

Turn a list of competitor domains into a landscape read: who is biggest, what each domain ranks for that the user does not, how their link profiles compare, and the two or three openings worth attacking first.

## Required inputs

- 2–5 competitor domains (`example.com`, no scheme).
- The user's own domain, so gaps are measured against something.
- Optional: country/language (defaults us/en).

## Before spending

- Call `citable_prices` (free) once per session.
- Per domain: $0.03 (overview) + $0.04 (keywords). Add $0.10 per domain only if the user wants the link angle. State the total for the whole set first — 4 domains with links is $0.68.
- Failed calls are never charged.

## Tools

- `citable_domain_overview(domain, country?, lang?)` — $0.03. Keywords ranked, top-3/top-10 counts, estimated traffic and its ad value.
- `citable_domain_keywords(domain, limit?, country?, lang?)` — $0.04. The domain's ranking keywords with volume, CPC, position, URL and last month's movement (previousPosition, change, status), loudest first.
- `citable_backlinks(domain, limit?)` — $0.10. Optional: link totals, domain rank, and the top referring domains.
- `citable_keyword_metrics(keywords, country?, lang?)` — $0.03. Optional: firm up volumes/difficulty for a shortlist before recommending it.
- `citable_serp(keyword, gl?, hl?, num?)` — $0.008. Optional: eyeball the live page for one contested keyword.

## Workflow

1. `citable_domain_overview` on every domain including the user's. Rank the set by keywords and estimated traffic — this is the size-of-army table, and it frames everything after it.
2. `citable_domain_keywords` on each competitor (`limit: 50`) and on the user's domain. Build the gap list: keywords where a competitor ranks top 10 and the user does not appear. Sort by volume.
3. If the user wants the link angle: `citable_backlinks` on each domain. Compare referring-domain counts and domain rank; list referrers that link to more than one competitor but not to the user — those are the prospecting targets.
4. Optional sharpening: `citable_keyword_metrics` on the top 10 gap keywords for difficulty and intent; `citable_serp` on the single most contested one to see what format wins the page.
5. Output:
   - The landscape table: domain · keywords ranked · top-10 count · est. traffic (· domain rank if links were bought).
   - The gap list: 10 keywords competitors win that the user does not, with volume and who holds them.
   - If links ran: shared referrers the user is missing, as a prospect list.
   - Two or three openings, each one sentence: the keyword or cluster, why it is winnable, and the first page to build.
   - Total spent, from the `_payment` fields.

## Guardrails

- Traffic numbers are index estimates, not analytics — present them as relative size, never as measured visits.
- Do not run `backlinks` on domains the user did not ask about; it is the expensive call in the set.
- Keep the whole set in one country/language; mixing locales makes the comparison meaningless.
