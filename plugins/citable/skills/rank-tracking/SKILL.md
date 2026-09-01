---
name: rank-tracking
description: "Track a site's Google positions for a keyword list over time without an account: the agent keeps the history in the repo, re-checks on a schedule, and reports what moved. $0.012 per keyword per check; the monthly trend of the whole domain is one $0.03 call."
---

# Rank tracking

## Goal

A rank tracker with no dashboard and no account: a keyword list in the repo, a history file next to it, one paid check per keyword per run, and a diff that says what moved. The agent is the tracker; the wallet pays per run; the history lives with the site it describes.

## Required inputs

- The domain to track.
- A keyword list — a file (`citable/keywords.txt`, one per line) or pasted. Ask for the market: country and language — `gl`/`hl` on `rank_check`, `country`/`lang` on `domain_history` and `domain_keywords` (default `us`/`en`).
- How often: on demand, or a schedule (daily / weekly). Never assume a schedule.

## Before spending

- `citable_prices` (free) once per session.
- A run costs $0.012 × keywords. Say the total before the first run ("20 keywords → $0.24 per run, $7.20 a month daily"). Ask before more than 30 keywords per run.
- For the whole domain's trend, `citable_domain_history` is $0.03 for up to 24 months — offer it first when the user has no keyword list yet.
- Failed calls are never charged; a keyword that errors is retried on the next run, not now.

## Tools

- `citable_rank_check(keyword, domain, gl?, hl?, num?)` — $0.012. `position` (null = outside the top `num`, default 20), the ranking `url`, and the top-10 list.
- `citable_domain_history(domain, months?, country?, lang?)` — $0.03. Monthly keywords ranked, top-10 count, traffic, and new/up/down/lost counts, with a first-to-last trend. Monthly resolution, index-wide.
- `citable_domain_keywords(domain, limit?, country?, lang?)` — $0.04. The keywords the domain ranks for, each with `previousPosition`, `change` and `status` from the index — a ready-made list of what to track, with last month's movement included.

## Files

- `citable/keywords.txt` — the list, one keyword per line. The user owns it; never add keywords without asking.
- `citable/ranks.jsonl` — one line per check: `{"at":"2026-08-29","keyword":"seo api","domain":"citable.run","position":4,"url":"…","tx":"…"}`. Append only; never rewrite history.
- Commit both with the site (`git add citable/`), so the history travels with the repo and survives machines.

## Workflow

1. Read `citable/keywords.txt` (or create it from the user's list). State the run cost.
2. For each keyword: `citable_rank_check`. Append one line per result to `citable/ranks.jsonl` with today's date and the `_payment.transaction`.
3. Diff against the most recent earlier line for each keyword. Report a table: keyword · position · change (▲ / ▼ / — / new) · ranking URL. Lead with what moved most; list unchanged keywords in one line.
4. If this is the first run, there is no diff: say so, and offer `citable_domain_history` for the domain-level trend so the user has a baseline today.
5. Total spent, from the `_payment` fields.

## Scheduling

The agent does not run on its own; the schedule lives where the agent does. Offer one, do not pick:
- **Claude Code routine / scheduled task** — "every weekday at 9:00, run /citable:rank-tracking and commit citable/ranks.jsonl".
- **GitHub Actions** — a cron workflow that runs the same skill through `citable-mcp` with a funded keypair in a repository secret (`CITABLE_WALLET` as the JSON byte array).
- **Plain cron** on the user's machine.
Whichever they choose, the wallet must hold enough USDC for the month; say the amount.

## Guardrails

- Never loop rank checks over `citable_domain_keywords` output without asking — 100 keywords is $1.20 per run.
- Positions from `citable_rank_check` are for the country/language given; changing `gl`/`hl` starts a new series — say so rather than mixing them.
- Do not "smooth" or infer positions for missed days; a gap in the file is a gap.
- `citable_domain_history` is monthly and index-wide: do not present it as daily keyword positions.
