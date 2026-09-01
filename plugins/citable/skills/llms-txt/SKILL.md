---
name: llms-txt
description: "Write a correct /llms.txt for the site in the working directory — what the site is, its key pages, contact — and serve it at the root so AI crawlers and answer engines have a map. Free; spends nothing."
---

# llms.txt

## Goal

Give AI crawlers a plain-text map of the site. `/llms.txt` is the first thing an answer engine can read without rendering anything; a site without one is asking to be summarised from whatever the crawler happens to fetch. This skill writes the file from the real site source and spends nothing.

## Required inputs

- The site's source in the working directory. If it is not here, ask for the domain and the list of pages that matter, and hand back file content the site owner can deploy.

## Workflow

1. Inventory the pages that exist: the framework's routes, the sitemap generator, or the content directory — whichever this repo actually has. Never list a page you cannot point to in the source.
2. Write `llms.txt` in this shape:

   ```
   # <Site name>

   > One sentence: what the site is and who it is for.

   ## Key pages
   - [Page title](https://domain/path): one line on what an agent finds there
   ...

   ## Contact
   - email or form URL
   ```

   Keep it under ~50 lines: the highest-value pages, not the whole sitemap. Docs, pricing, API reference, and "what is this" pages earn their place; legal boilerplate does not.
3. Serve it at the site root (`/llms.txt`, `text/plain`). Put the file wherever this repo's static assets live; if routes are code-defined, add the route. Confirm the build actually emits it.
4. If the site has deep documentation agents should read in full, also emit `llms-full.txt` with the expanded content — only when the user wants it, it is a second file to keep current.
5. Tell the user what was written and where, and that the file must be redeployed to exist publicly.

## Paid verification (optional)

After deploy, `citable_onpage_audit` on the homepage ($0.01) includes an `llms-txt` check that confirms the file is live and readable. Say the price and ask before running it.

## Guardrails

- Never invent pages, descriptions, or contact details — everything in the file must exist in the source.
- Leave out auth-gated and noindexed pages: pointing crawlers at what they cannot read wastes the map.
- One sentence per page. `llms.txt` is a map, not a pitch.
