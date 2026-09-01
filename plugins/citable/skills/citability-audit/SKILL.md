---
name: citability-audit
description: "Audit a page for citability — AI-crawler access, llms.txt, answer-shaped headings, structured data, classic on-page SEO — then apply the ordered fix list in the codebase and re-audit. $0.01 per audit."
---

# Citability audit

## Goal

Find out why a page is or isn't citable by search and AI answer engines, fix it, and prove the fix with a second audit. The audit returns an ordered work list; your job is to work through it, not to summarise it.

## Required inputs

- An absolute `http(s)` URL of one page. Ask if the user gave only a domain — audit the homepage first unless they name a page.
- Whether the site's source is in the working directory. If it is, you apply fixes; if not, you hand the user a fix list they can give to whoever owns the site.

## Before spending

- Call `citable_prices` (free) once per session. It returns the wallet, network, per-call prices and the spend cap (`CITABLE_MAX_PRICE`).
- One audit is $0.01. Ask before auditing more than 5 pages in one go.
- Failed calls are never charged. A paid result carries `_payment.transaction`, the settlement signature.

## Tools

- `citable_onpage_audit(url)` — $0.01. Returns `score` (0–100), `checks[]` (each with `id`, `status`, `weight`, `current`, `fix`) and `topFixes[]`, an ordered list of what to change first.

## Workflow

1. Run `citable_onpage_audit` on the URL. Read `score` and `topFixes[]`.
2. Report the score in one line, then the top fixes as a numbered list in the order the audit gives them — that order is by weight, so it is the order to work in.
3. If the source is available, apply fixes in that order. Typical mappings:

   | Check | Where it lives | Fix |
   |---|---|---|
   | `ai-crawler-access` | `robots.txt` | Allow `GPTBot`, `ClaudeBot`, `PerplexityBot`, `Google-Extended`, `CCBot` for the audited path. Say so explicitly if the user blocked one on purpose. |
   | `llms-txt` | `/llms.txt` at the site root | Plain-text summary: what the site is, its main pages, contact. |
   | `answer-headings` | The page's H2/H3s | Rewrite headings as the question a buyer would ask; put the direct answer in the first sentence under each. |
   | `article-signals` | Page template | Visible publish date and author. |
   | `js-content` | Rendering setup | The audit saw an empty shell. Server-render or prerender the page; AI crawlers do not run JavaScript. |
   | `structured-data` / schema checks | `<head>` JSON-LD | Match the page type: Article, Product, FAQPage, Organization. |
   | Title / meta length, H1, canonical, alt text | Page template | Follow the `fix` string on each check. |

4. After deploy (or against a preview URL), run `citable_onpage_audit` again.
5. Report before → after score, what you changed, and anything that needs a human: DNS, CMS settings, a crawler the user must decide about.

## Guardrails

- Never quote a score you did not get from the tool.
- Do not loosen `robots.txt` for a crawler the user deliberately blocked without saying so first.
- If `js-content` fails, do not try to "fix" it with more client-side JavaScript.
- One URL per audit. The audit is per page, not per site.
