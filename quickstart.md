---
title: Quickstart — BridgeNode
description: Send a paid chat completion to BridgeNode in two requests — an unpaid probe that returns a 402 price quote, then a signed retry.
---

# Quickstart

BridgeNode has no signup step. An agent needs a Solana wallet holding USDC and an x402-capable client. Every call follows the same two-step pattern: an unpaid request returns the exact price, then a signed retry returns the completion.

## Step 1 — probe for the price

Send the request without a payment header:

```bash
curl https://bridgenode.cc/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"hello"}],"max_tokens":100}'
```

The response is `402 Payment Required` with a `PAYMENT-REQUIRED` header (base64 JSON) containing the exact price in USDC atomic units, the `payTo` Solana address, and a memo.

## Step 2 — sign and retry

Construct the partial transaction (USDC `TransferChecked` + memo instruction), sign it with the agent's own wallet, and retry with the signature attached:

```bash
curl https://bridgenode.cc/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "PAYMENT-SIGNATURE: <base64 payload>" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"hello"}],"max_tokens":100}'
```

The response is `200` with the completion and a `PAYMENT-RESPONSE` header confirming settlement.

In practice, an x402-capable client (the official `x402` SDK, `x402-proxy`, or the `pay` CLI) automates both steps — it never returns a raw 402 to your application code, it handles the sign-and-retry internally.

## Python (x402 SDK)

```python
# pip install "x402[svm]"
# The official x402 client handles the full 402 -> sign -> retry loop.
```

Configure the client with the agent's Solana keypair and point it at `https://bridgenode.cc/v1/chat/completions`. Payment is automatic.

## Before you send a real request

Check current prices and context limits first — they are the source of truth for what a given model costs and can handle:

```bash
curl https://bridgenode.cc/v1/models
```

This endpoint is public and requires no payment. See [Models & Pricing](/models-pricing) for the current table.

## Next

- [Payment Flow](/payment-flow) for the full protocol detail (network, asset, transaction shape)
- [API Reference](/api-reference) for the complete request and response schema
- [Errors](/errors) for what each status code means
