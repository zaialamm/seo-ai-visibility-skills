---
name: schema-markup
description: "Add JSON-LD structured data that matches a page's actual type and visible content — Article, Product, FAQPage, Organization, SoftwareApplication — so search and AI engines can parse what the page is. Free; spends nothing."
---

# Schema markup

## Goal

Give each page machine-readable structure that mirrors what a human sees on it. Structured data is how engines learn a page's type, author, date, and price without guessing; wrong or fabricated schema is worse than none. This skill edits the site source and spends nothing.

## Required inputs

- The page (or template) to mark up, in the working directory. Ask which pages matter if the user gave only a repo.

## Workflow

1. Read the rendered content of the page and pick the type it visibly is — not the type the user wishes it were:

   | The page is | Type |
   |---|---|
   | A post or guide with an author and date | `Article` (or `BlogPosting`) |
   | A thing with a price | `Product` with `Offer` |
   | Question-and-answer sections | `FAQPage` — only if Q&A is visible on the page |
   | The company/about/home page | `Organization` (add `WebSite` on the home page) |
   | An app, API, or tool | `SoftwareApplication` |

2. Write one JSON-LD `<script type="application/ld+json">` block per type into the page's `<head>` or template. Every value comes from the page itself: the real headline, the visible date, the actual price and currency, the author who is named on the page.
3. If the template is shared, wire fields from the page's data (frontmatter, CMS fields, props) rather than hard-coding one page's values into every page.
4. Check the JSON parses and required properties for the type are present (`headline` + `datePublished` for Article; `name` + `offers.price` + `priceCurrency` for Product; `mainEntity` question/answer pairs for FAQPage).
5. Report what was added per page and any field that had no on-page source — those need the user, not an invented value.

## Paid verification (optional)

After deploy, `citable_onpage_audit` on the page ($0.01) includes structured-data checks. Say the price and ask before running it.

## Guardrails

- Never fabricate `aggregateRating`, `review`, or review counts. Rating markup without visible on-page reviews is a manual-action offence, not an optimisation.
- Schema must mirror visible content — no FAQPage markup for questions the page does not show, no dates the page does not display.
- One page, one set of blocks. Do not stamp `Organization` with full details on every page; the home or about page carries it.
