---
name: get-started
description: "How to use Citable well: set up the wallet, pick the right tool for the input you have (a topic, a keyword list, a domain, a URL, a buyer question), keep spend low, and read the receipts. Free — read this before the first paid call."
---

# Get started with Citable

## Goal

Make the first paid call the right one. Citable is 17 paid tools behind two free ones (`citable_prices`, `citable_connect`), and the usual mistakes are cheap individually but add up: sending a domain to a keyword tool, paying for three calls where one bundle is cheaper, running a $0.25 call to answer a $0.03 question. This skill spends nothing.

## Setup (once)

1. `citable_prices` — free. Returns every tool with its current price, plus the wallet that pays: `wallet` (address), `walletMode` (`keypair` or `sign-in`), `balanceUsdc`, `funded`. When it is empty it also returns `setup` with both ways to fund it.
2. If `funded` is false, **ask the user which they want — do not choose for them:**
   - **Sign in** (no crypto knowledge needed): call `citable_connect`, give the user the link. They sign in at citable.run with Google or email, add a few dollars, click *Connect to your agent*. When they say it is done, `citable_prices` shows `walletMode: sign-in` and the balance. The link lasts 15 minutes; `citable_connect` with `status: true` checks, `reset: true` disconnects.
   - **Keypair** (already uses Solana): the server created `~/.config/citable/agent.json` the first time it was needed; they send USDC on the **Solana** network to the `wallet` address (no SOL — the x402 facilitator pays fees), or set `CITABLE_WALLET` to a funded keypair file. $1 is ~200 keyword suggestions or ~30 domain overviews.
3. Cap it: `CITABLE_MAX_PRICE` (default 0.30) refuses any single call above that amount before anything is signed. Tell the user what it is set to.

Prices in tool descriptions are what is charged; if they differ from `citable_prices`, `citable_prices` wins. Prices change — quote from it, not from memory.

## Which tool for what you have

Pick by the *input*, not by the question.

| You have | Use | Price | Not this |
|---|---|---|---|
| A topic or phrase people type ("solana rpc") | `citable_keyword_suggest` — phrasings and questions, no volumes | $0.005 | a domain as the seed: it autocompletes the string literally |
| A topic, and you need volumes | `citable_keyword_research` — keywords + questions with volume, CPC, difficulty, intent | $0.06 | `keyword_ideas` + `keyword_metrics` separately: same data, costs more |
| A topic, volumes only, strictly on-topic | `citable_keyword_ideas` | $0.05 | |
| A list of 1–20 keywords | `citable_keyword_metrics` — one call per batch of 20 | $0.03 per batch | one call per keyword: 20× the price |
| A keyword + a domain | `citable_rank_check` — the domain's Google position plus the top 10 | $0.012 | |
| A keyword, and you want to see the page | `citable_serp` — organic results, People-Also-Ask, related searches | $0.008 | |
| A domain | `citable_domain_overview` first — keywords ranked, traffic, value | $0.03 | asking for its keywords before knowing its size |
| A domain, and you want its keywords | `citable_domain_keywords` — what it ranks for, with URLs and last month's movement per keyword | $0.04 | `keyword_suggest` with the domain as seed |
| A domain, and you want its rankings over time | `citable_domain_history` — monthly keywords, top-10 count, traffic, new/up/down/lost, with a trend. Nothing stored | $0.03 | re-running `rank_check` daily by hand — the `rank-tracking` skill does that with a history file |
| A domain, and you want its links | `citable_backlinks` — totals, domain rank, top referring domains | $0.10 | |
| One page URL | `citable_onpage_audit` — 0–100 score with an ordered fix list | $0.01 | a bare domain: it audits exactly one page |
| A buyer question **the user wrote** + a domain | `citable_ai_visibility` — which engines cite the domain for it | $0.05/engine | inventing the question — see guardrails |
| A domain, and you want what AI already cites it for | `citable_cited_prompts` — observed questions, snippets, cited URLs | $0.25 | |
| A domain, and you want its best pages in AI answers | `citable_top_cited_pages` | $0.22 | |
| A domain, and you want the trend over months | `citable_ai_mentions_trend` | $0.18 | |
| 2–10 domains to compare in AI answers | `citable_ai_share_of_voice` — one call for the whole set | $0.18 | one call per domain |
| A domain + a user-written question, full picture | `citable_citability_report` — audit + visibility + top pages | $0.30 | the three separately ($0.43) |

Country and language default to `us` / `en`. For any other market pass both (`country: id, lang: id`; `gl`/`hl` on `rank_check` and `serp`): the wrong market is a wasted call, not an error.

## Spend rules

- Say the price before every paid call and the running total after. `_payment.transaction` in each result is the on-chain receipt.
- Cheapest signal first: `domain_overview` ($0.03) before `domain_keywords` ($0.04) before `backlinks` ($0.10); `onpage_audit` ($0.01) before `ai_visibility` ($0.05 per engine).
- Batch where the tool allows it: `keyword_metrics` takes 20 keywords for one price; `ai_share_of_voice` takes 10 domains for one price.
- One call is enough. The exception is `ai_visibility`: engine answers vary between runs, so 2–3 runs give signal — say so before running more than one.
- Never re-run a call to "double-check" a result you already have; it is the same feed.
- Ask before any single call over $0.20, before passing about $1 in a session, and before any loop the running skill does not already cap (rank checks over a list, audits over a site).

## Reading results

- Any 4xx/5xx means nothing was charged. `payment refused before sending` means the spend cap or the balance stopped it — run `citable_prices`.
- `matchesSeed: false` in `keyword_suggest` means the phrase drifted from the seed: treat as no demand, not as an idea.
- Null metrics from `keyword_metrics` mean the index does not know the keyword: report "no data", not zero.
- `position: null` from `rank_check` means outside the checked window (default 20), not unranked.
- `cited_prompts`, `top_cited_pages`, `ai_mentions_trend` and `ai_share_of_voice` read an index of observed AI answers; `ai_mentions_trend` data begins 2025-08. A domain with no rows is unobserved, not penalised.

## Workflows on top of these tools

`seo-coach` (free) routes a symptom to one of `citability-audit`, `keyword-brief`, `keyword-clustering`, `ai-visibility-check`, `competitor-citations`, `competitive-landscape`, `rank-tracking`, or `full-check`, which chains them end to end under one budget consent. Use those when the user has a goal; use the table above when the user has an input. Four free build skills — `llms-txt`, `schema-markup`, `geo-rewrite`, `ai-crawler-access` — fix a site without spending anything; `citability-audit` verifies the result for $0.01.

## Guardrails

- Never generate a buyer prompt for `ai_visibility` or `citability_report`. If the user has none, `cited_prompts` on a competitor returns real ones.
- Never send a domain to a seed tool, or a bare domain to `onpage_audit`.
- Never quote a price from memory when `citable_prices` is one free call away.
- Do not spend to answer a question the user has not asked.
