---
title: MCP Usage — BridgeNode
description: Use BridgeNode as an MCP tool over streamable-http, with per-call x402 payment and a free list_models tool for price checks.
---

# MCP Usage

BridgeNode exposes an MCP server for MCP-compatible agents, in addition to its OpenAI-compatible REST endpoint.

| | |
|---|---|
| Server URL | `https://bridgenode.cc/mcp` |
| Transport | Streamable HTTP only — HTTP+SSE is deprecated (SEP-2596) |
| Payment | x402 handshake per tool call |

## `chat_completions` tool

**Input schema:**

| Field | Type | Notes |
|---|---|---|
| `model` | string | Explicit model id |
| `mode` | string | Enum: `auto`, `eco`, `premium` |
| `messages` | array | Required |
| `max_tokens` | integer | |

Description from the tool listing: *"Send a chat completion request to any supported model. Paid tool: x402 payment (Solana USDC) is required — the first call returns 402 with the exact price; retry with `_meta[\"x402/payment\"]`. Responses are non-streaming. Provide either `mode` (auto/eco/premium routing) or `model` (explicit id, mutually exclusive with mode); one of the two is required."*

Prices are annotated in `tools/list` under the `x-x402` extension as an indication — always check the actual amount in the `402` response before signing, since prices are dynamic.

To inspect models and prices first without paying, use the free `list_models` tool (equivalent to `GET /v1/models`, see [Models & Pricing](/models-pricing)).

## Discovery resource entry

BridgeNode's `/discovery/resources` endpoint (x402 Bazaar format) lists the MCP resource alongside the HTTP one:

```json
{
  "resource": "https://bridgenode.cc/mcp",
  "type": "mcp",
  "x402Version": 2,
  "accepts": [{
    "scheme": "exact",
    "network": "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp",
    "asset": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "payTo": "BHMDv3ri3LBEZjEzJgDZeUiguVX7LmsCstTXbM3dL8rN",
    "maxTimeoutSeconds": 30
  }]
}
```

See [Payment Flow](/payment-flow) for what `scheme: exact` means for signing and settlement.

## Supported clients

- Any MCP-compatible agent that supports Streamable HTTP transport
- Claude Code, Codex, Cursor, Windsurf (via the agent skill, see [SDKs & CLI](/sdks-cli))
- Any x402-capable client for the payment handshake itself
