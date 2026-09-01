---
name: ai-crawler-access
description: "Make robots.txt deliberate about AI crawlers: decide per bot whether it feeds answers or training, allow or block it explicitly, and keep the sitemap wired. Free; spends nothing."
---

# AI crawler access

## Goal

A site is cited only by engines that can read it, and most robots.txt files were written before AI crawlers existed. This skill turns the default (silence, which some bots read generously and others don't) into explicit per-bot decisions the user actually made. It edits the working directory and spends nothing.

## Required inputs

- The site source containing `robots.txt` (or the place it should exist). If the file is generated, find the generator — editing build output fixes nothing.

## Workflow

1. Read the current `robots.txt`. Note every existing rule; nothing here removes a rule the user wrote on purpose.
2. Put the decision to the user per crawler — what each one feeds is the whole tradeoff:

   | User-agent | Feeds | Blocking it means |
   |---|---|---|
   | `GPTBot` | OpenAI training and search index | ChatGPT is less likely to know or cite the site |
   | `ClaudeBot` | Anthropic training and retrieval | Claude is less likely to know or cite the site |
   | `PerplexityBot` | Perplexity's answer index | Absent from Perplexity answers |
   | `Google-Extended` | Gemini training only | Google Search and its rankings are unaffected |
   | `CCBot` | Common Crawl, which many models train on | Slowly absent from models trained on it |

   A site that wants citations allows the answer-feeding bots; blocking `Google-Extended` or `CCBot` is a defensible training-only stance. Both are choices — record whichever the user makes.
3. Write each decision as an explicit `User-agent:` block with `Allow: /` or `Disallow: /` (or the paths the user names). Explicit beats implicit: a bot named in the file is a decision; a bot missing from it is an accident.
4. Confirm `Sitemap:` lines point at sitemap URLs that exist and that the sitemap lists real, canonical pages. Add `/llms.txt` (the `llms-txt` skill writes it) if the site has one.
5. Report the before/after file and which lines carry a user decision versus a default.

## Paid verification (optional)

After deploy, `citable_onpage_audit` on the homepage ($0.01) includes the `ai-crawler-access` check. Say the price and ask before running it.

## Guardrails

- Never silently unblock a crawler the user deliberately blocked — surface it, ask, and record the answer.
- Never `Disallow: /` a bot as a "safe default" on a site that exists to be found.
- robots.txt is public: no comments in it about why a bot was blocked, no internal paths revealed by over-specific rules.
