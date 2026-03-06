---
icon: circle-dollar
---

# Stable Vault

The Stable Vault is Hako's current vault product for stablecoins.
It accepts supported stablecoins across supported networks, normalizes them into one shared accounting system,
and allocates capital into enabled external yield venues.

Base is the current home chain. `HakoStableVault` on Base is the source of truth for vault shares,
total managed assets, and withdrawal finalization.

## Overview

| Property             | Current behavior    |
|----------------------|---------------------|
| Home chain           | Base                |
| Home contract        | `HakoStableVault`   |
| Remote entrypoints   | `HakoStableGateway` |
| Vault token location | Base                |
| Deposit assets       | USDT, USDC, DAI     |

The Stable Vault is designed as one cross-chain stablecoin vault.
Deposit asset and withdrawal asset are not tied to each other.
Subject to allowlists and available payout liquidity, a user can deposit one supported stablecoin and request payout
in another supported stablecoin on a supported destination chain.

## Contract Model

There are two Hako-owned contract entrypoint types in the Stable Vault.

| Feature                           | `HakoStableVault`                              | `HakoStableGateway`                     |
|-----------------------------------|------------------------------------------------|-----------------------------------------|
| Where it is deployed              | Base                                           | Other EVMs                              |
| Main role                         | Canonical shares, accounting, withdrawal queue | Local deposit and withdrawal entrypoint |
| Vault shares mint and burn        | Yes                                            | No                                      |
| Tracks global managed assets      | Yes                                            | No                                      |
| Stores withdrawal requests        | Canonical request and final state              | Local payout request state              |
| Can hold local liquidity          | Yes                                            | Yes                                     |
| Can hold external-vault positions | Yes                                            | Yes                                     |

## Deposit Lifecycle

1. The user calls `deposit(...)` on the selected chain entrypoint.
2. A remote EVM deposit is first recorded on `HakoStableGateway` and emits `GatewayDepositRecorded(...)`.
3. A Base deposit mints LP shares on `HakoStableVault`.
4. The allocator later calls `recordRemoteDeposit(...)` on the home vault.
5. After that home recording step, the deposit is part of the canonical LP position on Base.

The important difference is that remote deposits are accepted locally, but the LP position still exists only on the home vault.

## Withdrawal Lifecycle

1. The canonical withdrawal request exists on the home vault.
2. The request locks LP shares on the home vault.
3. If the destination chain is Base, the request can be flagged for payout-on-complete on the home vault.
4. If the destination chain is remote, payout is completed first on the destination gateway.
5. In both cases, `completeWithdrawal(...)` on the home vault is still required to burn shares and close accounting.

Remote payout is not the final accounting step. Final accounting always happens on the home vault.

## Strategy Management

Stable Vault yield routing is managed operationally rather than through one public strategy contract.

- The current strategy is selected offchain from active & trusted external vaults.
- The allocator acts on the latest active strategy definition.
- Capital is deposited into or redeemed from external ERC-4626 vaults through allocator or admin-wallet transactions on the Hako contracts.

## Related Pages

- [Contract Schema](contract-schema.md)
- [Contract Addresses](contract-addresses.md)
- [Vault Allocations](../architecture/vault-allocations.md)
- [Withdrawal Queue](../architecture/withdrawal-queue.md)
