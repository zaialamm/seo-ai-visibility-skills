---
name: geo-rewrite
description: "Restructure a page so AI answer engines can lift and cite it: answer-shaped headings, the direct answer in the first sentence, self-contained quotable paragraphs, concrete facts. Free; spends nothing. Verifying the effect costs $0.05/engine."
---

# GEO rewrite

## Goal

Make a page liftable. Answer engines quote sections that stand alone: a heading that reads as the question, a first sentence that answers it, a paragraph that survives being extracted without its neighbours. This skill restructures existing content in the working directory — it does not change what the page claims, only how the claims are shaped. It spends nothing.

## Required inputs

- The page source in the working directory.
- Who the page is for, in one line — a rewrite aimed at nobody optimises for nothing.

## Workflow

1. Read the page. List its sections and, for each, the question a reader would have to ask for this section to be the answer. A section that answers no question is the first thing to fix or cut.
2. Rewrite each heading as that question (or a tight noun form of it), and move the direct answer into the first sentence under it. Explanation, caveats, and detail follow the answer — never precede it.
3. Make paragraphs self-contained: resolve "it/this/these" back to their nouns, keep one idea per paragraph, put concrete numbers, names, and dates in the sentence that makes the claim.
4. Add one liftable summary near the top — a two-to-four sentence block or a short fact list that answers "what is this and why does it matter" without reading further.
5. Keep what already works: the H1 and title keep their search intent, the voice stays the site's voice, and every factual claim stays exactly as factual as it was.
6. Show the user the restructure as a diff or before/after outline, not a wall of prose.

## Paid verification (optional)

The rewrite's effect is measurable only against a real buyer question. If the user has one, `citable_ai_visibility` (via the `ai-visibility-check` skill) shows which engines cite the domain for it — $0.05 per engine, checked before and after deploy. Say the price and ask first. If the user has no question, `citable_cited_prompts` on a competitor ($0.25) returns observed ones — never invent the question yourself.

## Guardrails

- Never change a factual claim, number, or promise while reshaping it. Restructuring is not rewriting the truth.
- No keyword stuffing and no question-heading spam — a page of twelve near-identical questions reads as generated and lifts worse, not better.
- If the content is rendered client-side, say so: engines that do not run JavaScript never see this rewrite (the `citability-audit` skill's `js-content` check confirms it for $0.01).
