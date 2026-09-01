# SEO & AI-visibility skills for coding agents

**Other skill packs tell your agent what to do. These come with the data.**

Thirteen skills that make a site rank in Google and get cited by ChatGPT, Claude, and Perplexity. Six are free and work offline on your own source. Seven are backed by live data — keyword volumes, Google rankings, backlinks, AI-citation checks — that your agent **buys per check** through [Citable](https://citable.run): $0.005–0.30 per call in USDC on Solana via [x402](https://www.x402.org). No API key, no account, no subscription, nothing stored about you. Failed calls are never charged, and every paid result carries its on-chain receipt.

That last part is the difference. A prompt-only "keyword research" skill has no volumes and a prompt-only "rank tracking" skill has no SERPs — they either ask you for an Ahrefs login or guess. These skills check.

## Install

**Claude Code**

```
/plugin marketplace add zaialamm/seo-ai-visibility-skills
/plugin install citable@seo-ai-visibility-skills
```

**Codex**

```
codex mcp add citable -- npx -y citable-mcp
```

**Cursor / Claude Desktop** — add to `mcp.json`:

```json
{ "mcpServers": { "citable": { "command": "npx", "args": ["-y", "citable-mcp"] } } }
```

The plugin runs the [`citable-mcp`](https://www.npmjs.com/package/citable-mcp) server locally; your wallet never leaves your machine. On platforms without MCP, point the agent at **https://citable.run/skill.md** — the same endpoints, paid over raw HTTP with any x402 client.

## Skills

**Free — spend nothing:**

| Skill | What it does |
|---|---|
| `/citable:get-started` | Setup, the input → tool map for all 17 paid tools, spend rules — read first |
| `/citable:seo-coach` | Answer three questions, get routed to the one workflow that fits, with its cost |
| `/citable:llms-txt` | Write a correct `/llms.txt` from the real site source |
| `/citable:schema-markup` | JSON-LD that matches what the page visibly is — no fabricated ratings |
| `/citable:geo-rewrite` | Restructure a page so answer engines can lift and cite it |
| `/citable:ai-crawler-access` | Explicit per-bot robots.txt decisions: GPTBot, ClaudeBot, PerplexityBot, Google-Extended, CCBot |

**Data-backed — pay per check:**

| Skill | What it does | Cost |
|---|---|---|
| `/citable:citability-audit` | Audit a page, apply the ordered fix list in your code, re-audit | $0.01 per audit |
| `/citable:ai-visibility-check` | Which AI engines cite a domain for *your* question, who they cite instead | $0.05–0.30 |
| `/citable:keyword-brief` | Seed → real volumes, difficulty, intent, current rank | ~$0.05–0.07 |
| `/citable:keyword-clustering` | Keyword list → page groups, build order, cannibalization check | $0.03 per 20 keywords |
| `/citable:competitor-citations` | One rival → the prompts AI cites them for, share of voice vs you | ~$0.65 |
| `/citable:rank-tracking` | Keyword list → positions per run, history diffed in your repo; schedule it | $0.012 per keyword per run |
| `/citable:competitive-landscape` | 2–5 rivals → organic footprint, winning keywords, link sources | ~$0.07–0.17 per domain |

Every paid skill states its price before spending and reports the running total. `CITABLE_MAX_PRICE` (default 0.30) refuses any single call above it before anything is signed.

## Wallet

Two ways to fund the agent — it tells you both on first use:

- **Sign in** (no crypto needed): the agent runs `citable_connect`, you open the link, sign in at citable.run with Google or email, add a few dollars, click Connect.
- **Keypair** (already on Solana): the server creates `~/.config/citable/agent.json` when first needed; fund it with USDC on Solana. No SOL required — the x402 facilitator covers network fees.

| Env | Default | Meaning |
|---|---|---|
| `CITABLE_WALLET` | `~/.config/citable/agent.json` | keypair path (or JSON byte array) holding USDC on mainnet |
| `CITABLE_MAX_PRICE` | `0.30` | refuse any single call priced above this many USD |
| `CITABLE_API` | `https://citable.run` | base URL |

## Links

Docs: https://citable.run/docs · API index: https://citable.run/api · Agent onboarding: https://citable.run/skill.md · MCP server: [`citable-mcp`](https://www.npmjs.com/package/citable-mcp)

MIT licensed. Maintained by [Citable](https://citable.run).
