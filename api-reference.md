---
title: API Reference — BridgeNode REST Endpoints
description: Request and response schema for POST /v1/chat/completions and GET /v1/models, BridgeNode's OpenAI-compatible REST API.
---

# API Reference

Base URL: `https://bridgenode.cc`. This reference is generated from BridgeNode's OpenAPI 3.1 spec (`GET /openapi.json`).

## `POST /v1/chat/completions`

Generate an AI chat completion, paid per request via x402. No API keys, no accounts, no subscriptions.

An unpaid request returns an x402 challenge (exact price in USDC atomic units, `payTo`, and memo); sign the Solana USDC transfer and retry with the `PAYMENT-SIGNATURE` header. See [Payment Flow](/payment-flow) for the full handshake.

**Security:** `x402` — API key-style header, name `PAYMENT-SIGNATURE`.

### Request body

| Field | Type | Required | Notes |
|---|---|---|---|
| `model` | string | one of `model`/`mode` | Enum: `deepseek-v4-flash`, `deepseek-v4-pro`, `groq-llama-3.3-70b`. Wins over `mode` if both are sent. |
| `mode` | string | one of `model`/`mode` | Enum: `auto`, `eco`, `premium`. |
| `messages` | array | yes, min 1 item | Chat messages so far. Each item: `role` (system/user/assistant/tool) and `content` (string). |
| `stream` | boolean | no | Enable SSE streaming. |
| `max_tokens` | integer | no, min 1 | Defaults to 4096, clamped to the model's max output. Non-stream requests are capped at 4096 — use `stream: true` for longer generations. |

```json
{
  "model": "deepseek-v4-flash",
  "mode": "auto",
  "messages": [{ "role": "user", "content": "Hello" }],
  "max_tokens": 128
}
```

### Responses

| Status | Meaning |
|---|---|
| `200` | Successful response — OpenAI-format chat completion object |
| `402` | Payment Required — x402 challenge in the `PAYMENT-REQUIRED` header |

### Pricing metadata (per model)

| Model | Input / 1M tokens | Output / 1M tokens |
|---|---|---|
| `deepseek-v4-flash` | $0.20 | $0.40 |
| `deepseek-v4-pro` | $0.50 | $1.00 |
| `groq-llama-3.3-70b` | $0.79 | $0.99 |

Per-request price range across all models: **$0.002 – $0.532**, computed dynamically from `model` and `max_tokens`.

## `GET /v1/models`

List the models BridgeNode serves with their per-token prices. **Public endpoint — no payment required.**

```bash
curl https://bridgenode.cc/v1/models
```

Response: `200` with a JSON object, `{ "object": "list", "data": [...] }`, where each entry carries `id`, `pricing.prompt`, `pricing.completion`, `context_window`, and `max_output_tokens`. See [Models & Pricing](/models-pricing) for the current values.

A `HEAD /v1/models` variant is also available for the same route.

## Discovery endpoints

| Path | Content |
|---|---|
| `/openapi.json` | Full OpenAPI 3.1 spec |
| `/discovery/resources` | x402 Bazaar resource listing (HTTP + MCP variants of `chat_completions`) |
| `/.well-known/agent-card.json`, `/.well-known/mcp.json`, `/.well-known/ai-manifest.json` | Agent/service discovery manifests |
| `/llms.txt` | Machine-readable install map for LLM agents |

## Errors

See the [Errors](/errors) page for the full status code table and error object format.
