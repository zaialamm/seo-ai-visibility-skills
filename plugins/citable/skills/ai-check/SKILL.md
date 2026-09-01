---
name: ai-check
description: "Check whether AI engines (ChatGPT, Claude, Gemini, Perplexity) cite a domain for a buyer question the user supplies, who they cite instead, and which of the domain's pages AI already cites. Never invents the prompt."
---

# AI visibility check

## Goal

Answer three questions with real engine output: Do AI answers cite this domain for this question? If not, who do they cite? And which of the domain's pages does AI already cite, so the user builds on what works.

## Required inputs

- The domain (hostname only, e.g. `example.com`).
- **The exact question, from the user.** Engines run on the user's prompt, not one you draft. If they have not given one, ask: *"What question would a buyer type into ChatGPT that you want to be cited for?"* If they want candidates, run `citable_keyword_suggest` and show its `questions[]` — the user picks, you do not.

## Before spending

- Call `citable_prices` (free) once per session for wallet, network, prices and the spend cap.
- State the cost before running: `citable_ai_visibility` $0.05 per engine ($0.20 for all four), `citable_top_cited_pages` $0.22, `citable_citability_report` $0.30 (audit + visibility on all engines + top pages together; $0.43 bought separately).
- Answers vary between runs of the same domain + prompt; 2–3 runs give signal. Ask before the second run, and state the total.
- Failed calls are never charged.

## Tools

- `citable_ai_visibility(prompt, domain, engines?)` — $0.05 per engine asked, $0.20 for all four. Per engine: whether the domain was cited, the citations, and the competing domains cited instead.
- `citable_top_cited_pages(domain, limit?)` — $0.22. The domain's pages ranked by how often AI answers cite them, with AI search volume.
- `citable_citability_report(domain, prompt, limit?)` — $0.30. All three in one call when the user wants the full picture.

## Workflow

1. Confirm domain + prompt + cost. If the user wants the full picture, use `citable_citability_report` instead of steps 2–3.
2. Run `citable_ai_visibility`. Build a table: engine · cited? · top citations.
3. Collect the domains cited instead across engines and count them — the one that appears most is the competitor to study.
4. Run `citable_top_cited_pages` on the user's domain when they are not cited, or when they ask what already works. Show page · mentions · AI volume.
5. Optional, ask first ($0.22): `citable_top_cited_pages` on the top competitor, to see which of *their* pages win and in what format.
6. End with one next action. Usually one of:
   - not cited, page exists → run the `citability-audit` skill on that page;
   - not cited, no page → write the page the competitor wins with, in answer shape;
   - cited by some engines → audit the page for the engines that skipped it.

## Guardrails

- One prompt per run; prompts are ≤ 400 characters.
- Say "not cited" only when the engine answered and did not cite the domain. If an engine errored or was not configured, say that instead.
- Never present the engines' answers as the user's ranking. Citation is presence in the answer's sources, not position.
