---
title: Errors — BridgeNode API
description: BridgeNode status codes and the OpenAI-format error object returned on failed requests.
---

# Errors

| Status | Meaning |
|---|---|
| `400` | Bad request — unknown model, invalid body, or oversized non-stream `max_tokens` |
| `402` | Payment required — see the `PAYMENT-REQUIRED` header for price, `payTo`, and memo |
| `413` | Request body too large — limit is 2 MB |
| `429` | Too many requests — queue limit reached |
| `503` | Service busy — retry with backoff |

All errors use the OpenAI error format:

```json
{
  "error": {
    "message": "...",
    "type": "...",
    "code": "..."
  }
}
```

## Empty answers are not an error

A `200` response with empty `content` is not a failure state on BridgeNode's side — it means the model consumed its entire `max_tokens` budget on reasoning and had nothing left for the visible answer. This is common with thinking models when `max_tokens` is set too low.

- Empty answers are **not refunded** (the provider returned `200`; the service was provided).
- Fix: increase `max_tokens` to at least 200 and submit a new paid request.

See [Models & Pricing → Reasoning models](/models-pricing) for the full explanation.

## Refunds

If the provider fails **before** any content is delivered, the payment is refunded automatically via a reverse USDC transfer. See [Payment Flow](/payment-flow) for the settlement mechanics.
