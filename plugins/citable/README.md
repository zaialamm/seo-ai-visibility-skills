# Citable plugin

SEO and AI-visibility data agents can buy. Every tool pays for itself in USDC on Solana over x402 — no API key, no subscription, nothing stored about you. $0.005–0.30 per call; failed calls are never charged.

## Install

**Claude Code**

```
/plugin marketplace add zaialamm/Citable
/plugin install citable@citable
```

**Codex**

```
codex mcp add citable -- npx -y citable-mcp
```

**Cursor / Claude Desktop** — add to `mcp.json`:

```json
{ "mcpServers": { "citable": { "command": "npx", "args": ["-y", "citable-mcp"] } } }
```

The plugin runs the [`citable-mcp`](https://www.npmjs.com/package/citable-mcp) server locally. Your wallet never leaves your machine.

## Wallet

The server pays from one of two wallets and tells the agent about both on install. **Sign in** (no crypto needed): the agent runs `citable_connect`, you open the link, sign in at citable.run with Google or email, add a few dollars, click Connect. **Keypair** (already on Solana): with nothing configured the server creates `~/.config/citable/agent.json` when first needed; `citable_prices` shows the address to fund with USDC on Solana.

| Env | Default | Meaning |
|---|---|---|
| `CITABLE_WALLET` | `~/.config/citable/agent.json` | keypair JSON path (or the JSON byte array) holding USDC on mainnet; created when first needed if missing |
| `CITABLE_MAX_PRICE` | `0.30` | refuse any single call priced above this many USD — nothing is signed |
| `CITABLE_API` | `https://citable.run` | base URL |

No SOL needed: the x402 facilitator covers network fees. Set env vars in the shell that launches your client; MCP servers inherit them.

## Skills

| Skill | What it does | Cost |
|---|---|---|
| `/citable:start` | The front door: wallet setup, symptom → workflow routing, the input → tool map for all 17 paid tools, spend rules — read first | free |
| `/citable:llms-txt` | Write a correct `/llms.txt` from the real site source | free |
| `/citable:schema-markup` | JSON-LD matching what the page visibly is — no fabricated ratings | free |
| `/citable:geo-rewrite` | Restructure a page so answer engines can lift and cite it | free |
| `/citable:ai-crawler-access` | Explicit per-bot robots.txt decisions for the AI crawlers | free |
| `/citable:full-check` | The whole engagement: baseline → fixes → demand data → verify → monitoring, one budget consent | $0.05 / ~$0.27 / ~$1.15 by tier |
| `/citable:citability-audit` | Audit a page, apply the ordered fix list in your code, re-audit | $0.01 per audit |
| `/citable:ai-check` | Which AI engines cite a domain for *your* question, who they cite instead, which pages already work | $0.05–0.30 |
| `/citable:keyword-brief` | Seed → real volumes, difficulty, intent, current rank | ~$0.05–0.07 |
| `/citable:keyword-clustering` | Keyword list → page groups, build order, cannibalization check against current rankings | $0.03 per 20 keywords |
| `/citable:competitor-citations` | One rival → the prompts AI cites them for, the pages doing the work, share of voice vs you | ~$0.65 |
| `/citable:rank-tracking` | Keyword list → positions per run, history kept in your repo, a diff of what moved; schedule it where the agent lives | $0.012 per keyword per run |
| `/citable:competitive-landscape` | 2–5 rival domains → organic footprint, the keywords each wins, where their links come from | ~$0.07–0.17 per domain |

## Tools

| Tool | Price |
|---|---|
| `citable_prices` | free |
| `citable_keyword_suggest` | $0.005 |
| `citable_keyword_research` | $0.06 |
| `citable_keyword_ideas` | $0.05 |
| `citable_keyword_metrics` | $0.03 |
| `citable_rank_check` | $0.012 |
| `citable_serp` | $0.008 |
| `citable_onpage_audit` | $0.01 |
| `citable_ai_visibility` | $0.05/engine |
| `citable_top_cited_pages` | $0.22 |
| `citable_cited_prompts` | $0.25 |
| `citable_ai_mentions_trend` | $0.18 |
| `citable_ai_share_of_voice` | $0.18 |
| `citable_citability_report` | $0.30 |
| `citable_domain_overview` | $0.03 |
| `citable_domain_keywords` | $0.04 |
| `citable_domain_history` | $0.03 |
| `citable_backlinks` | $0.10 |

Every paid result carries `_payment.transaction`, the on-chain settlement signature.

Docs: https://citable.run · API index: https://citable.run/api · Agent onboarding: https://citable.run/skill.md
