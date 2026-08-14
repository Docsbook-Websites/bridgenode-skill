---
title: Payment Flow — x402 on Solana
description: How BridgeNode's x402 V2 exact-scheme payment handshake works — the 402 challenge, signed USDC transfer, and settlement receipt.
---

# Payment Flow

BridgeNode uses the **x402 V2 protocol**, **exact scheme**, settled in USDC on Solana mainnet. There are no accounts and no stored balances — every request is paid individually.

## The handshake

1. The agent sends the request without payment headers.
2. The server responds `402 Payment Required` with a `PAYMENT-REQUIRED` header (base64 JSON): price, `payTo` address, USDC mint, memo, and a recent blockhash.
3. The agent constructs a **partial transaction** — a USDC `TransferChecked` instruction for the required amount plus a `Memo` instruction — and signs it with its own wallet. The agent does **not** sign as fee payer.
4. The agent retries the request with a `PAYMENT-SIGNATURE` header: a base64-encoded JSON payload carrying the signed transaction.
5. The server verifies the payment and processes the request. Transaction fees are sponsored by BridgeNode, so the flow is gasless for the agent.
6. The response is `200` with a `PAYMENT-RESPONSE` header containing the settlement receipt.

## Network and asset

| | |
|---|---|
| Network | `solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` (Solana mainnet) |
| Asset | USDC — `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| Scheme | `x402-exact` |
| Security scheme | `apiKey` in header `PAYMENT-SIGNATURE` |

The agent must already have a USDC associated token account (ATA) for the mint — it's derived from the agent's wallet address, no manual setup required.

## Pricing model

Pricing follows the **exact scheme**: the agent pays for `input tokens + max_tokens` **before** the model processes the request. If the model generates fewer tokens than `max_tokens`, the agent still pays for the full `max_tokens` allocation — this is the billing model, not a bug.

Per-request price range: **$0.002 – $0.532** (dynamic, computed from `model` and `max_tokens`). The minimum charge on any request is 2000 atomic units ($0.002 USDC).

## Funding

- The agent keeps USDC in its own wallet. BridgeNode never holds balances — every request settles individually via x402.
- Gasless: BridgeNode's sponsor covers Solana transaction fees, so the agent needs zero SOL.
- Optional client-side spending limits, enforced locally by the SDK before signing (not server-side balances): `BRIDGENODE_MAX_PER_CALL`, `BRIDGENODE_DAILY_CAP`.

## Refunds

If the provider fails before any content is delivered, the payment is refunded automatically via a reverse USDC transfer. If the provider returns `200` with an empty answer (see [Reasoning Models](/quickstart) note on `max_tokens`), the request is considered served and is **not** refunded.

## Clients that implement this for you

Use an official x402 SDK (`@x402/svm`, `x402[svm]`) or any x402-capable client — they handle the 402 → sign → retry loop automatically so application code never has to construct the Solana transaction by hand.
