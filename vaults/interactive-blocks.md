---
description: 'Super Stable is Hako core vault focused on stablecoins: USDT, USDC, DAI.'
icon: circle-dollar
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Super Stable

#### Key Features

* Aggregates stablecoins into one common vault.
* Allocates them into stable strategies on multiple protocols and chains.
* Uses NEAR Intents to swap and bridge stablecoins when capital needs to move.
* Rebalances the portfolio over time based on where yield and liquidity are available.

You still only see one position in the app: your Super Stable LP balance and its value. All the underlying strategy movements happen in the background.

#### Supported assets and networks

Super Stable accepts the main USD stablecoins (USDT, USDC, DAI) on multiple networks:

* _Ethereum_
* _Polygon_
* _Base_
* _Arbitrum_
* _Optimism_
* _Solana (coming soon)_

#### Where funds go

The Super Stable vault allocates only into **stablecoin-focused strategies**.

Liquidity from the vault is deposited into stablecoin vaults and products built on top of protocols and curators:

* [Morpho](https://docs.morpho.org/get-started/) – whitelisted lending and liquidity vaults that allocate deposits across lending markets.
* [Yearn](https://docs.yearn.fi/) – route assets into DeFi strategies and automate rebalancing.
* [Avantis](https://docs.avantisfi.com/tutorials/vaults) – on-chain derivatives and liquidity strategies built on Base.
* [Steakhouse](https://www.steakhouse.financial/docs) – stablecoin-oriented, institution-grade DeFi tooling and vault curation. &#x20;
* [Clearstar](https://www.clearstar.xyz/) – delta-neutral and yield-focused strategies for institutional-grade DeFi portfolios.

Plus other selected stablecoin vaults and protocols that fit the same profile.

{% hint style="info" %}
The vault does not use this capital to speculate on volatile tokens; it keeps exposure in stablecoin-based strategies (even if some of those strategies provide liquidity to more complex markets in the background).
{% endhint %}

#### Main risks

Even though the underlying assets are stablecoins, the strategy is not risk-free. Key risk categories include:

1. **Stablecoin risk.** Stablecoin loses its peg or faces issues, the vault’s value is affected.
2. **Protocol risk.** Any of the integrated vaults or protocols could suffer a bug, exploit, or liquidity event.
3. **Cross-chain and bridge risk.** Super Stable relies on bridging to move capital between chains; issues with bridges or target chains can impact withdrawals and allocations.
4. **Operational risk.** Rebalancing and allocation decisions are made by Hako's infrastructure. Operational mistakes or key management failures can cause losses.

{% hint style="info" %}
Users should only deposit amounts they are comfortable putting into a DeFi yield product with this risk profile.
{% endhint %}
