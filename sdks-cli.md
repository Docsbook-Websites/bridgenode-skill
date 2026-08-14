---
title: SDKs & CLI — BridgeNode
description: Official Python, TypeScript, and CLI clients for BridgeNode, all handling the x402 payment handshake automatically.
---

# SDKs & CLI

All official clients handle the x402 payment handshake automatically — request, `402`, sign, retry, `200` — with fail-closed spending limits enforced client-side before signing.

## Python SDK

```bash
pip install bridgenode-llm
```

Package: [pypi.org/project/bridgenode-llm](https://pypi.org/project/bridgenode-llm)

For the full toolkit (not just the LLM client):

```bash
pip install bridgenode
```

## CLI

```bash
pip install bridgenode-cli
```

Package: [pypi.org/project/bridgenode-cli](https://pypi.org/project/bridgenode-cli)

```bash
bridgenode chat "Hello!"
```

## TypeScript SDK

```bash
npm i @bridgenode/llm
```

Package: [npmjs.com/package/@bridgenode/llm](https://www.npmjs.com/package/@bridgenode/llm)

## x402 SDK (payment layer only)

If you're wiring payment into a custom client rather than using a BridgeNode SDK, the official x402 client for Solana handles the 402 → sign → retry loop:

```bash
pip install "x402[svm]"
```

Configure it with the agent's Solana keypair and point it at `https://bridgenode.cc/v1/chat/completions`; payment happens automatically. JavaScript/TypeScript equivalent: `@x402/svm`.

## Agent skill

For coding agents (Claude Code, Codex, Cursor, Windsurf) that support the `skills` install format:

```bash
npx skills add applefanaimail-blip/bridgenode-skill
```

Source: [github.com/applefanaimail-blip/bridgenode-skill](https://github.com/applefanaimail-blip/bridgenode-skill)

## Spending limits

All SDKs support optional, client-side spending limits — enforced locally before a transaction is signed, not against a server-side balance:

| Variable | Purpose |
|---|---|
| `BRIDGENODE_MAX_PER_CALL` | Cap on USDC spent per single request |
| `BRIDGENODE_DAILY_CAP` | Cap on total USDC spent per day |

## Discovery for agent frameworks

- Agent card: `/.well-known/agent-card.json`
- MCP manifest: `/.well-known/mcp.json`
- AI manifest: `/.well-known/ai-manifest.json`
- Full install map: [`/llms.txt`](https://bridgenode.cc/llms.txt)
- Listed on [x402-list](https://x402-list.com/services/bridgenode) and [ClawHub](https://clawhub.ai/bridgenode/skills/bridgenode)
