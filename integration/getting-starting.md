---
description: This page helps partners reach a first successful Hako integration quickly.
icon: handshake
---

# Getting Started

#### Base URL

```
http://app.hakoapp.app/v1
```

#### Auth Model

_Hako_ uses API key authentication. Expected request header:

```http
X-API-Key: <YOUR_PARTNER_API_KEY>
```

#### Request Example

```bash
curl -X POST "<BASE_URL>/v1/quotes/deposit" \
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

#### 5-Minute Happy Path

1. Confirm service health.
2. Fetch strategy metadata.
3. Request a deposit quote.
4. Create a deposit action.
5. Track action status until terminal state.

#### Useful links

* Integration API - [api](api/ "mention")
* Contract Schema - [contract-schema.md](../vaults/contract-schema.md "mention")
* Contract Addresses - [contract-addresses.md](../vaults/contract-addresses.md "mention")&#x20;
