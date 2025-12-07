---
description: >-
  The withdrawal queue is the part of Hako that manages withdrawals over time.
  It keeps track of user requests, locks the corresponding LP tokens and
  coordinates liquidity movements across chains.
icon: line-height
---

# Withdrawal Queue

When you request a withdrawal in the app, you only sign a message. &#x20;

The signed request is picked up by the allocator, sent to the `home vault` as an on-chain transaction, and turned into an entry in the withdrawal queue.&#x20;

**Then, the allocator engine:**

1. Locks the requested part of your LP position in the home vault.
2. Prepares liquidity by redeeming from external vaults and moving funds through NEAR Intents.
3. Sends tokens to your address on the chosen destination chain.
4. Updates the home vault to reflect that this portion of your position has been paid out and burned.

{% hint style="info" %}
The queue exists so that all of this can be done in order, in batches, and in sync with vault allocations and cross-chain movements.
{% endhint %}

```mermaid
sequenceDiagram
  participant U as User wallet
  participant APP as Hako app
  participant AL as Allocator
  participant HV as Home vault
  participant EX as External vaults
  participant NI as Hako NEAR Intents
  participant DST as User address<br>(terget chain)

  U->>APP: Fill amount, chain, token to withdraw
  APP->>U: Show typed data
  U-->>APP: Signed EIP-712<br>withdrawal message

  APP-->>AL: Forward signed message
  AL->>HV: Submit withdrawal signed data
  Note over HV: Verify signature,<br>create queue entry,<br>lock LP tokens
  HV-->>AL: Withdrawal request event

  AL->>EX: Redeem from external vaults
  EX-->>HV: Return stablecoins<br>to Hako vaults

  AL->>HV: Approve Near Intent<br>to spend required amount
  HV->>NI: Transfer stablecoins

  AL->>NI: Swap/bridge to target chain
  NI->>DST: Send requested stablecoin

  AL->>HV: Finalize withdrawal tx
  Note over HV: Burn locked LP tokens,<br>update total assets
```



