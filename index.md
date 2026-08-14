---
title: BridgeNode — AI Inference Bridge for Agents
description: BridgeNode is an OpenAI-compatible inference API for AI agents that pay per request with Solana USDC via x402 — no API keys, no accounts.
---

# BridgeNode

BridgeNode is an AI inference bridge built exclusively for AI agents. There are no API keys, no registration, and no subscriptions — every request is paid individually with Solana USDC through the [x402](https://github.com/coinbase/x402) payment protocol.

## Endpoints

| Endpoint | Purpose |
|---|---|
| `https://bridgenode.cc/v1` | OpenAI-compatible API base URL |
| `GET https://bridgenode.cc/v1/models` | Models and prices (public, no payment required) |
| `POST https://bridgenode.cc/v1/chat/completions` | Chat completions (x402 paid) |
| `https://bridgenode.cc/mcp` | MCP server (x402-paid tools, streamable-http) |
| `https://bridgenode.cc/llms.txt` | Machine-readable install map for agents |

## Models

| Model | Input (per 1M) | Output (per 1M) |
|---|---|---|
| `deepseek-v4-flash` | $0.20 | $0.40 |
| `deepseek-v4-pro` | $0.50 | $1.00 |
| `groq-llama-3.3-70b` | $0.79 | $0.99 |

Prices are quoted live from `GET /v1/models` — see [Models & Pricing](/models-pricing) for the full table with context windows and max output.

## SDKs

- **Python:** `pip install bridgenode-llm` ([PyPI](https://pypi.org/project/bridgenode-llm)) — full toolkit: `pip install bridgenode`
- **CLI:** `pip install bridgenode-cli` ([PyPI](https://pypi.org/project/bridgenode-cli))
- **TypeScript:** `npm i @bridgenode/llm` ([npm](https://www.npmjs.com/package/@bridgenode/llm))

## Agent skill

Install directly into an agent with:

```bash
npx skills add applefanaimail-blip/bridgenode-skill
```

Source: [github.com/applefanaimail-blip/bridgenode-skill](https://github.com/applefanaimail-blip/bridgenode-skill)

## Where to go next

- [Quickstart](/quickstart) — send your first paid request in two calls
- [Payment Flow](/payment-flow) — how the x402 handshake works end to end
- [API Reference](/api-reference) — full request and response schemas
- [MCP Usage](/mcp) — using BridgeNode as an MCP tool
- [SDKs & CLI](/sdks-cli) — official client libraries
- [Errors](/errors) — status codes and error format
- [Terms of Service](/terms)
