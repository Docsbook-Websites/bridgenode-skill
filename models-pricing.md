---
title: Models & Pricing — BridgeNode
description: Current BridgeNode model list with per-token USDC pricing, context window, and max output — always confirmed live via GET /v1/models.
---

# Models & Pricing

Prices are BridgeNode prices in USDC per 1M tokens, paid per request via x402 on Solana — no API keys.

| Model | Input (per 1M) | Output (per 1M) | Context window | Max output |
|---|---|---|---|---|
| `deepseek-v4-flash` | $0.20 | $0.40 | 1,048,576 | 8,192 |
| `deepseek-v4-pro` | $0.50 | $1.00 | 1,048,576 | 8,192 |
| `groq-llama-3.3-70b` | $0.79 | $0.99 | 131,072 | 32,768 |

## Always confirm live

`GET /v1/models` is the source of truth — it is public and requires no payment:

```bash
curl https://bridgenode.cc/v1/models
```

```json
{
  "object": "list",
  "data": [
    { "id": "deepseek-v4-flash", "pricing": {"prompt": 0.0000002, "completion": 0.0000004}, "context_window": 1048576, "max_output_tokens": 8192 },
    { "id": "deepseek-v4-pro", "pricing": {"prompt": 0.0000005, "completion": 0.000001}, "context_window": 1048576, "max_output_tokens": 8192 },
    { "id": "groq-llama-3.3-70b", "pricing": {"prompt": 0.00000079, "completion": 0.00000099}, "context_window": 131072, "max_output_tokens": 32768 }
  ]
}
```

## Routing modes

Instead of naming a `model` explicitly, a request can pass `mode`:

| Mode | Behavior |
|---|---|
| `auto` | Complexity-based tier selection |
| `eco` | Cheapest available model |
| `premium` | Best available model |

If both `model` and `mode` are present in the same request, `model` wins.

## Reasoning models

Thinking/reasoning models generate reasoning tokens that **share the `max_tokens` budget** with the final answer.

- Use `max_tokens >= 200`. A limit that's too small can be fully consumed by reasoning, producing a `200` response with an **empty answer**.
- An empty answer is not refunded — the service was provided (the provider returned `200`). Increase `max_tokens` and retry with a new payment.
- Prefer `stream: true` for long generations; non-streaming responses are capped at 4096 output tokens.
- If tool calls are used with a thinking model, `reasoning_content` must be echoed back on the next turn or the API returns `400`.

See [Payment Flow](/payment-flow) for how the exact-scheme price is computed from `max_tokens`.
