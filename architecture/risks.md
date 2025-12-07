---
description: >-
  Using Hako involves risk. This page summarizes the main categories so you can
  understand what you are exposed to when you deposit into a Hako vault.
icon: traffic-cone
---

# Risks

Hako cannot guarantee profit or principal protection. Vault returns and capital safety depend on many external protocols, stablecoins, networks, and on the correct operation of our own contracts and infrastructure.

{% hint style="info" %}
Nothing in this section is financial advice. Always do your own research and only deposit what you are prepared to risk in a DeFi strategy.
{% endhint %}

#### Smart contract risk

Hako uses custom smart contracts for vaults, accounting, withdrawal queues, and integrations with external protocols. Bugs or design mistakes in these contracts could lead to loss of funds, stuck withdrawals, or incorrect accounting.

#### Operational risk

Most movements of pooled liquidity (between vaults, NEAR Intents, and external strategies) are executed by **`Admin Wallet`** controlled by the **Hako** team. If this wallet or its keys were compromised, an attacker could try to misuse the same operational permissions the allocator relies on, within the protocol allowed paths.

{% hint style="info" %}
We plan to migrate the Hako Admin Wallet to an **MPC setup**, where control is split across multiple parties and no single key is enough to operate it.
{% endhint %}

#### External protocol and stablecoin risk

Hako allocates capital into external stablecoin strategies and uses stablecoins like USDT, USDC and DAI. Each protocol and each stablecoin has its own risk profile: contracts can be exploited, governance can fail, liquidity can dry up, or a stablecoin can lose its peg. If that happens, the portion of Hako assets exposed to that protocol or stablecoin may be lost or devalued, even if Hako's own contracts behave correctly.

#### Bridge and liquidity risk

Hako uses NEAR Intents as a cross-chain routing layer to move tokens between chains and to users. Any bridge or cross-chain system adds risk: contracts can have bugs, infrastructure can fail, and chain-level issues can delay or block transfers. Withdrawals also depend on liquidity in external vaults and money markets; during stress, withdrawals may take longer than usual or be temporarily unavailable until liquidity is restored.

#### How to think about these risks

> Hako is designed for users who understand that DeFi yield comes with real risk, are comfortable delegating strategy selection and cross-chain routing to an automated system operated by a team, and can tolerate potential delays or losses in extreme scenarios. Before depositing, consider your own tolerance for smart contract, stablecoin, bridge and operational risk.



<br>
