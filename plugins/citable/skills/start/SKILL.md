---
name: start
description: "Start here, free: set up the wallet once, then get routed. A problem ('AI never mentions us') goes through three questions to the one workflow that fits, with its cost; data in hand (a topic, a keyword list, a domain, a URL) maps straight to the right tool. Spend rules and how to read results included — read before the first paid call."
---

# Start

## Goal

One front door. Set the wallet up once, then pick the right next thing exactly once: a symptom routes to one workflow, an input maps to one tool. Citable is 17 paid tools behind two free ones (`citable_prices`, `citable_connect`), and the usual mistakes are cheap individually but add up: sending a domain to a keyword tool, paying for three calls where one bundle is cheaper, running a $0.25 call to answer a $0.03 question — or running every workflow when one was the answer. This skill spends nothing.

## Setup (once)

1. `citable_prices` — free. Returns every tool with its current price, plus the wallet that pays: `wallet` (address), `walletMode` (`keypair` or `sign-in`), `balanceUsdc`, `funded`. When it is empty it also returns `setup` with both ways to fund it.
2. If `funded` is false, **ask the user which they want — do not choose for them:**
   - **Sign in** (no crypto knowledge needed): call `citable_connect`, give the user the link. They sign in at citable.run with Google or email, add a few dollars, click *Connect to your agent*. When they say it is done, `citable_prices` shows `walletMode: sign-in` and the balance. The link lasts 15 minutes; `citable_connect` with `status: true` checks, `reset: true` disconnects.
   - **Keypair** (already uses Solana): the server created `~/.config/citable/agent.json` the first time it was needed; they send USDC on the **Solana** network to the `wallet` address (no SOL — the x402 facilitator pays fees), or set `CITABLE_WALLET` to a funded keypair file. $1 is ~200 keyword suggestions or ~30 domain overviews.
3. Cap it: `CITABLE_MAX_PRICE` (default 0.30) refuses any single call above that amount before anything is signed. Tell the user what it is set to.

Prices in tool descriptions are what is charged; if they differ from `citable_prices`, `citable_prices` wins. Prices change — quote from it, not from memory.

## Have a problem, not a plan? Route it

Ask, in one message, only what is missing:

1. What do you sell, and to whom? (one line)
2. Your domain — and the page you care about most, if there is one.
3. What is wrong today? Pick the closest: *nobody finds us · AI never mentions us · we don't know what to write · a competitor is everywhere · we rank but the wrong page shows up.*

Then choose one row. State the skill, why it fits their answer, the cost, and what they will get back in one sentence. Ask for a go, invoke it — do not re-explain it — and after it finishes suggest at most one follow-up, with its cost. When two rows apply, choose the cheaper one first and say what the second would add.

| The user says | Run | Typical cost |
|---|---|---|
| "Just do everything" / a full engagement with a budget | `full-check` — baseline → fixes → demand data → verify, one consent | $0.05 / ~$0.27 / ~$1.15 by tier |
| "AI never mentions us" and they have a buyer question in mind | `ai-check` | $0.05 per engine per question |
| "AI never mentions us" but they don't know what buyers ask | `competitor-citations` on a rival — it returns the observed prompts | ~$0.65 |
| "We don't know what to write" | `keyword-brief` on their topic | ~$0.05 |
| "We have a keyword list" or "we rank with the wrong page" | `keyword-clustering` | $0.03 per 20 keywords |
| "A competitor is everywhere" in AI answers | `competitor-citations` | ~$0.65 |
| "Who are we really up against" / sizing a market | `competitive-landscape` on the set | ~$0.07–0.17 per domain |
| "Are we going up or down" / "track our rankings" | `rank-tracking` — keyword list + history in the repo, or `citable_domain_history` for the domain trend | $0.012 per keyword per run · $0.03 |
| "Nobody finds us" and they name a page | `citability-audit` on that page first — cheapest signal | $0.01 |
| "Nobody finds us" and no page in mind | `citability-audit` on the homepage, then `keyword-brief` | ~$0.07 |

Building or fixing the site itself is free: `llms-txt`, `schema-markup`, `geo-rewrite`, and `ai-crawler-access` work on the source without spending anything; `citability-audit` proves the result for $0.01.

## Have data in hand? Map it to a tool

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

## Guardrails

- Routing spends nothing. If the user says "just do it", still name the skill and the cost in the same message you start it.
- Never generate a buyer prompt for `ai_visibility` or `citability_report`. If the user has none, `cited_prompts` on a competitor returns real ones.
- Never send a domain to a seed tool, or a bare domain to `onpage_audit`.
- Never quote a price from memory when `citable_prices` is one free call away.
- Do not spend to answer a question the user has not asked.
- Do not route to workflows Citable does not have (link building, local SEO, site-wide crawls) — say plainly that those are not covered yet.
