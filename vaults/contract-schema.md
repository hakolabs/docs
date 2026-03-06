---
icon: memo-circle-check
---

# Contract Schema

This page describes the public end-user methods of the current Stable Vault contracts.
Internal controller, allocator, and admin methods are intentionally omitted from this page.
Use the proxy addresses from [Contract Addresses](contract-addresses.md).

## Contract Topology

- `HakoStableVault` is the home contract on Base. It is the canonical contract for vault shares, total managed assets, and withdrawal finalization.
- `HakoStableGateway` is deployed on the other supported EVM chains. It is the local deposit and payout entrypoint on those chains.
- Vault shares exist only on `HakoStableVault`.

## `HakoStableVault`

`HakoStableVault` is the contract users interact with on Base.
It mints and burns vault shares, stores the canonical withdrawal queue, and finalizes accounting for every completed withdrawal.

### `deposit`

```solidity
function deposit(address token, uint256 amount, address receiver)
    external
    returns (uint256 sharesMinted)
```

Deposits an allowlisted stablecoin on Base and mints vault shares for `receiver`.

| Parameter  | Type      | Description                                                             |
|------------|-----------|-------------------------------------------------------------------------|
| `token`    | `address` | Deposit token address. The token must be allowlisted in the home vault. |
| `amount`   | `uint256` | Deposit amount in the token's native decimals.                          |
| `receiver` | `address` | Address that receives the minted vault shares.                          |

| Event                                 | Event data                                                                                                                                                                   | Description                                                                  |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| ERC20 `Transfer` on the deposit token | `from`, `to`, `value`                                                                                                                                                        | Transfers the deposited stablecoin from the user to the home vault.          |
| Vault share `Transfer`                | `from`, `to`, `value`                                                                                                                                                        | Mints vault shares by transferring from `0x0` to `receiver`.                 |
| `DepositRecorded`                     | `depositId`: unique deposit id<br>`receiver`: share receiver<br>`amountNormalized`: deposit value in 18 decimals<br>`sharesMinted`: minted share amount<br>`remote`: `false` | Confirms that the home vault accepted the deposit and minted shares locally. |

### `requestWithdrawal`

```solidity
function requestWithdrawal(
    address receiver,
    uint64 dstChainId,
    address token,
    uint256 amountNormalized,
    uint256 maxShares
) external returns (uint256 requestId)
```

Creates a withdrawal request by target normalized asset amount and locks the required share amount.

| Parameter | Type | Description |
| --- | --- | --- |
| `receiver` | `address` | Destination EVM address that should receive the payout. |
| `dstChainId` | `uint64` | Destination chain identifier. The destination chain must be allowlisted. |
| `token` | `address` | Destination payout token. The token must be allowlisted for the selected destination chain. |
| `amountNormalized` | `uint256` | Requested withdrawal value in normalized 18-decimal units. |
| `maxShares` | `uint256` | Maximum number of vault shares the caller allows the contract to lock for this request. |

| Event | Event data | Description |
| --- | --- | --- |
| `WithdrawalRequested` | `requestId`: withdrawal request id<br>`owner`: share owner<br>`dstChainId`: destination chain<br>`token`: payout token<br>`receiver`: encoded destination receiver bytes<br>`amountNormalized`: requested value in 18 decimals<br>`sharesLocked`: number of locked shares | Confirms that the withdrawal request was created and the corresponding shares were locked. |
| `WithdrawalPayoutOnCompleteMarked` | `requestId`: withdrawal request id<br>`dstChainId`: destination chain<br>`token`: payout token | Emitted only when the destination chain is the home chain and payout will be executed during final completion. |

### `requestRedeem`

```solidity
function requestRedeem(
    address receiver,
    uint64 dstChainId,
    address token,
    uint256 shares,
    uint256 minAmountNormalized
) external returns (uint256 requestId)
```

Creates a withdrawal request by exact share amount instead of target value.

| Parameter             | Type      | Description                                                                                 |
|-----------------------|-----------|---------------------------------------------------------------------------------------------|
| `receiver`            | `address` | Destination EVM address that should receive the payout.                                     |
| `dstChainId`          | `uint64`  | Destination chain identifier. The destination chain must be allowlisted.                    |
| `token`               | `address` | Destination payout token. The token must be allowlisted for the selected destination chain. |
| `shares`              | `uint256` | Exact amount of vault shares to lock and redeem.                                            |
| `minAmountNormalized` | `uint256` | Minimum acceptable withdrawal value in normalized 18-decimal units.                         |

| Event                              | Event data                                                                                                                                                                                                                                                                | Description                                                                                                    |
|------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `WithdrawalRequested`              | `requestId`: withdrawal request id<br>`owner`: share owner<br>`dstChainId`: destination chain<br>`token`: payout token<br>`receiver`: encoded destination receiver bytes<br>`amountNormalized`: resulting value in 18 decimals<br>`sharesLocked`: number of locked shares | Confirms that the withdrawal request was created from an exact share amount.                                   |
| `WithdrawalPayoutOnCompleteMarked` | `requestId`: withdrawal request id<br>`dstChainId`: destination chain<br>`token`: payout token                                                                                                                                                                            | Emitted only when the destination chain is the home chain and payout will be executed during final completion. |

### `totalAssets`

```solidity
function totalAssets() external view returns (uint256)
```

Returns the total managed assets of the home vault in normalized 18-decimal units.

### `balanceOf`

```solidity
function balanceOf(address account) external view returns (uint256)
```

Returns the current vault share balance of `account`.

| Parameter | Type | Description |
| --- | --- | --- |
| `account` | `address` | Address whose vault share balance should be returned. |

### `withdrawalNonce`

```solidity
function withdrawalNonce(address owner) external view returns (uint256)
```

Returns the current withdrawal nonce used by the controller-based withdrawal flow for `owner`.

| Parameter | Type | Description |
| --- | --- | --- |
| `owner` | `address` | Address whose withdrawal nonce should be returned. |

### `getWithdrawalRequest`

```solidity
function getWithdrawalRequest(uint256 requestId)
    external
    view
    returns (
        address owner,
        address receiver,
        uint64 dstChainId,
        address token,
        uint256 amountNormalized,
        uint256 sharesLocked,
        WithdrawalStatus status
    )
```

Returns the stored home-vault withdrawal request data for `requestId`.

| Parameter | Type | Description |
| --- | --- | --- |
| `requestId` | `uint256` | Withdrawal request identifier returned when the request was created. |

### `getWithdrawalReceiver`

```solidity
function getWithdrawalReceiver(uint256 requestId)
    external
    view
    returns (bytes memory)
```

Returns the raw encoded receiver data stored for `requestId`.
For direct EVM withdrawals this is the encoded destination address.

| Parameter | Type | Description |
| --- | --- | --- |
| `requestId` | `uint256` | Withdrawal request identifier returned when the request was created. |

### `minDepositNormalized`

```solidity
function minDepositNormalized() external view returns (uint256)
```

Returns the current minimum accepted deposit value in normalized 18-decimal units.

## `HakoStableGateway`

`HakoStableGateway` is the chain-local contract users interact with on the supported non-home EVM chains.
It accepts deposits locally and stores local payout requests on that chain.

### `deposit`

```solidity
function deposit(address token, uint256 amount, address receiver)
    external
```

Deposits an allowlisted stablecoin into the local gateway contract.
This method does not mint vault shares locally. The deposit is later recorded on the home vault.

| Parameter | Type | Description |
| --- | --- | --- |
| `token` | `address` | Deposit token address. The token must be allowlisted on the gateway. |
| `amount` | `uint256` | Deposit amount in the token's native decimals. |
| `receiver` | `address` | EVM identity that will be credited on the home vault after remote deposit recording. |

| Event | Event data | Description |
| --- | --- | --- |
| ERC20 `Transfer` on the deposit token | `from`, `to`, `value` | Transfers the deposited stablecoin from the user to the gateway contract. |
| `GatewayDepositRecorded` | `depositId`: unique deposit id<br>`sender`: original caller<br>`token`: deposit token<br>`amountToken`: raw token amount<br>`amountNormalized`: deposit value in 18 decimals<br>`receiver`: receiver identity for home-vault accounting | Confirms that the gateway accepted the local deposit and created the remote deposit record. |

### `requestWithdrawal`

```solidity
function requestWithdrawal(address token, uint256 amountToken, address receiver)
    external
    returns (uint256 requestId)
```

Creates a local gateway withdrawal request for payout on the current chain.

| Parameter | Type | Description |
| --- | --- | --- |
| `token` | `address` | Payout token address. The token must be allowlisted on the gateway. |
| `amountToken` | `uint256` | Requested payout amount in the token's native decimals. |
| `receiver` | `address` | Destination EVM address that should receive the payout on this chain. |

| Event | Event data | Description |
| --- | --- | --- |
| `GatewayWithdrawalRequested` | `requestId`: withdrawal request id<br>`owner`: request owner<br>`receiver`: destination address<br>`token`: payout token<br>`amountToken`: raw token amount<br>`amountNormalized`: value in 18 decimals | Confirms that the local gateway withdrawal request was created. |

### `getWithdrawalRequest`

```solidity
function getWithdrawalRequest(uint256 requestId)
    external
    view
    returns (
        address owner,
        address receiver,
        address token,
        uint256 amountToken,
        uint256 amountNormalized,
        GatewayWithdrawalStatus status
    )
```

Returns the stored gateway withdrawal request data for `requestId`.

| Parameter | Type | Description |
| --- | --- | --- |
| `requestId` | `uint256` | Gateway withdrawal request identifier returned when the request was created. |

### `getWithdrawalReceiver`

```solidity
function getWithdrawalReceiver(uint256 requestId)
    external
    view
    returns (bytes memory)
```

Returns the raw encoded receiver data stored for `requestId`.
For current EVM gateway withdrawals this maps to the encoded destination address.

| Parameter | Type | Description |
| --- | --- | --- |
| `requestId` | `uint256` | Gateway withdrawal request identifier returned when the request was created. |

### `withdrawalNonce`

```solidity
function withdrawalNonce(address owner) external view returns (uint256)
```

Returns the current gateway withdrawal nonce used by the controller-based withdrawal flow for `owner`.

| Parameter | Type | Description |
| --- | --- | --- |
| `owner` | `address` | Address whose gateway withdrawal nonce should be returned. |

### `minDepositNormalized`

```solidity
function minDepositNormalized() external view returns (uint256)
```

Returns the current minimum accepted deposit value on the gateway in normalized 18-decimal units.
