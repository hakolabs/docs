---
description: >-
  Swap or bridge into a token and route that is currently available in the Hako
  app before you deposit.
icon: right-left
---

# Swap

_Hako's_ Swap page is a helper step for getting into a token and route that you can use in the Hako app today.

It uses the [NEAR Intents Widget](https://docs.near-intents.org/getting-started/what-are-intents) to simplify cross-chain swaps and bridges. Instead of manually bridging and swapping step by step, you choose the result you want and review the route shown in the widget.

The exact routes you see in Hako depend on the chains and tokens currently exposed by the app. If a route does not appear, it is not currently available through Hako's configured swap flow.

## Example

<figure><img src="../.gitbook/assets/Screenshot 2026-03-03 at 17.05.08.png" alt="" width="563"><figcaption></figcaption></figure>

## How the flow works

1. Choose the asset and network you currently hold.
2. Choose the asset and network you want to end up with.
3. Review the quote, route, estimated amount received, and fees.
4. Confirm the swap or bridge in your wallet.
5. When the swap completes, return to [Deposit](deposit.md) and continue with your deposit.

{% hint style="warning" %}
Quotes, available routes, and fees can change before confirmation. Always verify the destination token, network, and final amount before approving the transaction.
{% endhint %}
