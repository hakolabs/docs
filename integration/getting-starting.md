---
description: This page helps partners reach a first successful Hako integration quickly.
icon: handshake
---

# Getting Started

This page is the fastest path to Hako integration.
It explains how to get access, where to start in the API, and what the first deposit and withdrawal flow looks like.

## Base URL

```text
https://app.hakolabs.app/v1/
```

- Swagger / API docs: `https://app.hakolabs.app/v1/docs`
- OpenAPI spec: `https://app.hakolabs.app/v1/openapi.yml`

## Before You Start

- Requests are authenticated with `X-API-Key`.
- Action creation is idempotent per partner through `externalId`, so each deposit or withdrawal request should use its own partner-side idempotency key.

## Get API Key

To request a partner API key and start integration, contact Hako here:

- Email: [contact@hakolabs.app](mailto:contact@hakolabs.app)
- Telegram: [@rolaman](https://t.me/rolaman)

## Authentication

Hako uses API key authentication. Expected request header:

```http
X-API-Key: <YOUR_API_KEY>
```

## Integration Path

For a successful integration, use this sequence:

1. Check service availability with `GET /v1/health`.
2. Read available strategies with `GET /v1/strategy` and `GET /v1/strategy/:strategyId`.
3. Request a quote with `POST /v1/quotes/deposit` or `POST /v1/quotes/withdraw`.
4. Create an action with `POST /v1/action/deposit` or `POST /v1/action/withdraw`.
5. For deposits, let the user send the onchain transaction and then call `POST /v1/action/:actionId/report`.
6. For withdrawals, let the user sign the returned typed data and then call `POST /v1/action/:actionId/authorize`.
7. Poll `GET /v1/action/:actionId` until the action reaches a terminal state.

## First Authenticated Request

Example deposit quote request:

```bash
curl -X POST "https://app.hakolabs.app/v1/quotes/deposit" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>" \
  -d '{
    "strategyId": "stable_vault",
    "fromAccount": "0x123...",
    "assetId": "USDC",
    "network": "base",
    "amount": "12.5"
  }'
```

## Flows

- Deposit Flow: [deposit-flow.md](deposit-flow.md)
- Withdrawal Flow: [withdrawal-flow.md](withdrawal-flow.md)

## Integration Example

A reference integration example will live here:

- [Example Project](https://github.com/hakolabs/hako-integration-example)
