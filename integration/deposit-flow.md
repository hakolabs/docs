---
description: Learn the deposit flow end to end, with both CLI and direct HTTP examples.
icon: cart-plus
---

# Deposit Flow

This page covers the Hako-specific part of a deposit integration:
prepare the action, submit the onchain transactions, report the deposit tx hash, and verify the resulting share position.

{% hint style="info" %}
This page is intentionally workflow-first.
For the full request and response contract, use the published [API](api/README.md) page or CLI example [Github](https://github.com/hakolabs/hako-integration-example).
{% endhint %}

The Hako deposit flow has a few important rules:

* `POST /v1/action/deposit` is the required execution step and returns the prepared calldata
* `approvalTx` is optional, but `transaction` is always the deposit call that must be sent
* partners must call `POST /v1/action/:actionId/report` after the deposit tx is sent
* vault shares are canonical on Base, even when the deposit starts on a remote EVM chain

## Integration Endpoints

These are the main partner endpoints for deposits:

| Touchpoint                         | Why you use it                                                    |
|------------------------------------|-------------------------------------------------------------------|
| `GET /v1/strategy/:strategyId`     | Discover supported networks, tokens, and entrypoint contracts     |
| `POST /v1/quotes/deposit`          | Optionally preview the expected shares and timing                 |
| `POST /v1/action/deposit`          | Create the deposit action and get prepared transaction payloads   |
| `POST /v1/action/:actionId/report` | Tell Hako which onchain deposit transaction belongs to the action |
| `GET /v1/action/:actionId`         | Track the action lifecycle                                        |
| `GET /v1/position`                 | Confirm the resulting vault share position                        |

## How Deposit Settlement Works

Every deposit starts on the selected entrypoint contract, but the final vault share position exists only on Base.

* On **Base**, the deposit transaction calls `HakoStableVault.deposit(...)` and the deposit completes on the home vault.
* On a **remote EVM chain**, the deposit transaction calls `HakoStableGateway.deposit(...)` first.
* Hako then finalizes that remote deposit on the Base home vault. That is why remote deposits can stay in `COMPLETING` before they become `COMPLETED`.

For a deeper contract-level explanation, use [Contract Schema](../vaults/contract-schema.md)

## Step-by-Step

Using `stable_vault`, `base`, and `USDC` for the first successful integration path.

### 1. Inspect the strategy

**CLI**

```bash
npm run strategy:list
```

**HTTP**

```bash
curl "https://app.hakolabs.app/v1/strategy/stable_vault" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>"
```

### 2. Optionally request a quote

The quote step is recommended for onboarding and preflight checks.
It does not create an action.

**CLI**

```bash
npm run quote:deposit -- --chain base --token USDC --amount 25 --address 0x_your_wallet
```

**HTTP**

```bash
curl -X POST "https://app.hakolabs.app/v1/quotes/deposit" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>" \
  -d '{
    "strategyId": "stable_vault",
    "fromAccount": "0x_your_wallet",
    "assetId": "USDC",
    "network": "base",
    "amount": "25"
  }'
```

### 3. Create the deposit action

This is the API step that prepares the onchain transactions.

**CLI**

If you want one command that creates the action, sends the transactions, and reports the tx hash, use:

```bash
npm run deposit -- --chain base --token USDC --amount 25
```

**HTTP**

```bash
curl -X POST "https://app.hakolabs.app/v1/action/deposit" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>" \
  -d '{
    "strategyId": "stable_vault",
    "externalId": "partner-deposit-001",
    "address": "0x_your_wallet",
    "network": "base",
    "amount": "25",
    "token": "USDC"
  }'
```

The fields that matter most are:

* `id`
* `approvalTx`
* `transaction`

`approvalTx` may be `null`.
`transaction` is always the actual deposit call that must be sent onchain.

### 4. Sign and send the transactions

If the action includes `approvalTx`, send it first.
Then send the deposit transaction from `action.transaction`.

**Wallet code**

```ts
let approvalTxHash: string | null = null;

if (action.approvalTx) {
  approvalTxHash = await walletClient.sendTransaction({
    account,
    to: action.approvalTx.tx.to,
    data: action.approvalTx.tx.data,
    value: BigInt(action.approvalTx.tx.value),
  });

  await publicClient.waitForTransactionReceipt({
    hash: approvalTxHash,
    confirmations: 1,
  });
}

const depositTxHash = await walletClient.sendTransaction({
  account,
  to: action.transaction.tx.to,
  data: action.transaction.tx.data,
  value: BigInt(action.transaction.tx.value),
});

await publicClient.waitForTransactionReceipt({
  hash: depositTxHash,
  confirmations: 1,
});
```

After the deposit transaction is sent, your backend needs to call `POST /v1/action/:actionId/report`.

### 5. Report the deposit transaction hash

The report step attaches the real onchain transaction hash to the action.

**HTTP**

```bash
curl -X POST "https://app.hakolabs.app/v1/action/<ACTION_ID>/report" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>" \
  -d '{
    "network": "base",
    "txHash": "0xYourDepositTxHash"
  }'
```

### 6. Track completion and check the resulting position

Use the action to track the lifecycle.
Use the position endpoint to confirm the resulting vault share balance and inspect any remaining pending actions.

**CLI**

```bash
npm run action:get -- --action-id <ACTION_ID>
npm run action:watch -- --action-id <ACTION_ID>
npm run position -- --address 0xYourWallet
```

**HTTP**

```bash
curl "https://app.hakolabs.app/v1/action/<ACTION_ID>" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>"
```

```bash
curl "https://app.hakolabs.app/v1/position?address=0xYourWallet&strategyId=stable_vault" \
  -H "X-API-Key: <YOUR_PARTNER_API_KEY>"
```

## Status and Completion

Deposit actions use these states:

| Status       | Meaning                                                                            |
|--------------|------------------------------------------------------------------------------------|
| `NEW`        | The action exists, but the deposit tx hash has not been reported yet.              |
| `PROCESSING` | The tx hash was reported and Hako is waiting for canonical deposit processing.     |
| `COMPLETING` | A remote deposit was observed and is still being finalized on the Base home vault. |
| `COMPLETED`  | The deposit is finalized.                                                          |
| `FAILED`     | The source transaction failed or became invalid.                                   |

Important notes:

* If `/report` is never called, the action stays in `NEW`.
* `COMPLETING` is expected for remote deposits.
* A remote deposit is only fully finished when it reaches `COMPLETED`.
