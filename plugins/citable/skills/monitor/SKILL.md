---
name: monitor
description: "Put rankings and AI citations on a standing weekly watch: each run checks the keyword list and the buyer questions, diffs against last run, keeps history in the repo, and stops at a hard per-run budget. Setup spends nothing; a typical run costs $0.60–2.10."
---

# Monitor

## Goal

Turn one-off measurement into a standing watch. Setup writes a `citable/monitor.json` config and schedules a weekly run; each run answers two questions — *did rankings move?* and *did AI answers change who they cite?* — against last week, not against nothing. Setup spends nothing; only scheduled runs spend, and never more than the budget in the config.

## Two modes

- **Setup** (a person asked for a monitor): gather inputs, quote the recurring cost, write the config, install the schedule. No paid calls.
- **Run** (`monitor run`, invoked by the scheduler): read `citable/monitor.json`, execute the run contract below, append history, report the diff. No questions asked, nothing outside the config.

## Setup

Gather, in one message, only what is missing:

1. The domain, and the keyword list to track — or take it from `rank-tracking`'s history or a `keyword-brief` result if one exists in the repo.
2. Buyer questions **the user wrote** (0–10) and which engines, for the AI-citation leg. No questions → the leg is off, not invented.
3. Cadence — default weekly. Daily re-buys engine noise; say so if asked.

Then quote the standing cost before anything is scheduled:

```
per run  =  keywords × $0.012  +  questions × engines × $0.05
```

State it as $/run and $/month, check the wallet covers at least 4 runs (`citable_prices`, free), and get one yes — it covers every future run **at this config**. Any change to keywords, questions, engines, or cadence is a new quote.

Write `citable/monitor.json`: domain, keywords, questions, engines, cadence, `budgetPerRunUsd`. Then install the schedule where the agent lives:

| Where | How |
|---|---|
| Claude Code, machine usually on | a cron line running `claude -p "/citable:monitor run"` in this repo, weekly |
| Claude Code cloud | a scheduled routine (`/schedule`) with the prompt "run /citable:monitor run in <repo>" |
| GitHub Actions | a weekly workflow running the agent with this plugin; `CITABLE_WALLET` as a repo secret — a **dedicated key funded with ≤ $5**, never a main wallet — plus the agent's own auth secret; history commits back to the repo |

Show the user exactly what was installed and the first scheduled date.

## The run contract (run mode)

1. `citable_rank_check` over the keyword list; `citable_ai_visibility` per question × engines. Same parameters every run — the config is the contract.
2. Before each leg, check the running total against `budgetPerRunUsd`. Over budget → skip the rest, report what was skipped. Never exceed, never ask mid-run.
3. Append results to `citable/rank-history.json` and `citable/visibility-history.json` (or `rank-tracking`'s existing file if present).
4. Diff against the previous run: keywords up/down/entered/dropped with old → new positions; engines that newly cite or stopped citing the domain per question, and who displaced whom.
5. Report one summary: the movers, anything lost, spend this run (sum of `_payment.transaction` receipts), and runway — wallet balance ÷ cost per run ≈ N runs left. Under 4 runs, lead with the top-up warning.

## Guardrails

- The budget in the config is a ceiling, not a target. A failed call is never charged; a skipped leg is reported, not silently dropped.
- Single runs of `ai_visibility` are snapshots — flag a citation change as confirmed only when it holds for two consecutive runs.
- CI wallets are small and dedicated: ≤ $5 on a key that exists only for this monitor. Never put a treasury or daily-use key in a repo secret.
- If a run cannot pay (balance, cap), the report says the monitor is **stalled** — a monitor that silently stops watching is worse than none.
- Never add keywords, questions, or engines inside a run. Config changes happen in setup mode, with a fresh quote.
