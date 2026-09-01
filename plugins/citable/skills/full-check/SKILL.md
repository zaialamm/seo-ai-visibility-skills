---
name: full-check
description: "The whole engagement in one run: baseline the site, apply every fix the audit orders, pull the demand data, verify after deploy, and leave monitoring behind. One budget consent up front — $0.05, ~$0.27, or ~$1.15 by tier — then no per-call asks."
---

# Full check

## Goal

Do end to end what the individual skills do piecemeal: measure, fix, verify, and keep watching — with one price agreed before anything is spent. This skill orchestrates the others; it adds no tool of its own, and its output is one report: what the site scored, what changed, what it costs to know more.

## Required inputs

Ask, in one message, only what is missing:

1. The domain, and the page that matters most (default: homepage).
2. What they sell and to whom, in one line — it seeds the keyword work.
3. A buyer question **the user wrote**, if they have one — required for the visibility legs, never invented.
4. Whether the site's source is in the working directory (fixes get applied) or not (fixes get listed).

## Before spending

Quote the tiers with real prices from `citable_prices`, let the user pick one, and treat that yes as consent for **every call listed in the tier** — including any single call over $0.20 — and for nothing else:

| Tier | What runs | Paid calls |
|---|---|---|
| **page** | overview → audit → free fixes → re-audit | $0.03 + $0.01 + $0.01 ≈ **$0.05** |
| **standard** | page + keyword research, rank check on the top 5, visibility before/after on their question (1 engine) | + $0.06 + $0.06 + $0.10 ≈ **$0.27** |
| **full** | standard with 3 engines, + `competitor-citations` on one named rival | ≈ **$1.15** |

Standard and full need the user-written buyer question; without one, run page tier and offer `competitor-citations` ($0.65) to surface real questions. Anything beyond the tier — more pages, more keywords, extra visibility runs — is a new ask at its listed price.

## Workflow

1. **Baseline.** `citable_domain_overview` ($0.03) for size and traffic; `citable_onpage_audit` on the money page ($0.01). Standard and up: `citable_ai_visibility` on the user's question ($0.05/engine), `citable_keyword_research` on the seed ($0.06), `citable_rank_check` on the top 5 keywords by volume ($0.012 each). Save the numbers — score, positions, engines citing — to a small JSON in the repo (next to `rank-tracking`'s history file if one exists): this is the before.
2. **Fix.** Work the audit's `topFixes[]` in order, exactly as `citability-audit` does. Where a fix has a build skill, use it: `ai-crawler-access` for robots decisions, `llms-txt` for the map, `schema-markup` for structured data, `geo-rewrite` when headings and answers are the problem. All free. No source in the working directory → deliver the ordered fix list instead and stop after step 3.
3. **Demand read** (standard and up). From the keyword research: which keywords the page should target, where the domain already ranks, and — full tier — what AI cites the rival for (`competitor-citations`, ~$0.65) and what that implies for this site's pages.
4. **Deploy gap.** Verification only means anything against the live (or preview) URL. If deploy happens in-session, continue; if not, report the baseline + applied fixes and say exactly what to run after deploy: re-audit ($0.01) and, standard and up, the same visibility check ($0.05/engine).
5. **Verify.** Re-run the audit and the visibility question. Report before → after: score delta, engines that cite the domain now versus before, positions if enough time has passed for them to mean anything (say so if it hasn't).
6. **Leave monitoring behind.** Offer the `monitor` skill on the keywords and question from this run — a scheduled weekly rank + AI-citation watch with a hard per-run budget. That is the standing version of everything this run measured once.

Close with the report: baseline, changes, after, total spent (sum the `_payment.transaction` receipts), and the one next thing worth paying for.

## Guardrails

- The tier consent is the spend ceiling. Nothing outside the picked tier runs without a new price-and-ask.
- Never invent the buyer question — no question, no visibility leg.
- One `ai_visibility` run per leg is a snapshot, not a trend: engine answers vary between runs. Say so; 2–3 runs ($0.05/engine each) are an add-on, not a default.
- Don't re-run any call whose result this run already has; the verify legs exist because the *site* changed, not to double-check the feed.
- If the audit's `js-content` check fails, say the verify legs will keep failing until rendering is fixed — do not spend on visibility checks for a page crawlers cannot read.
