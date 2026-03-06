# Table of contents

* [Introduction](README.md)
* [Supported Networks](supported-networks.md)

## Learn

* [Getting Started](learn/quickstart.md)
* [Deposit](learn/deposit.md)
* [Withdrawal](learn/withdrawal.md)
* [Swap](learn/swap.md)

## Architecture

* [Vault Allocations](architecture/vault-allocations.md)
* [Withdrawal Queue](architecture/withdrawal-queue.md)
* [Risks](architecture/risks.md)
* [Roadmap](architecture/roadmap.md)

## Integration

* [Getting Starting](integration/getting-starting.md)
* [Deposit Flow](integration/deposit-flow.md)
* [Withdrawal Flow](integration/withdrawal-flow.md)
* [API](integration/api/README.md)
  * ```yaml
    props:
      models: true
      downloadLink: true
    type: builtin:openapi
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: hako-api
    ```

## VAULTS

* [Contract Schema](vaults/contract-schema.md)
* [Stable Vault](vaults/stable-vault.md)
* [Contract Addresses](vaults/contract-addresses.md)
* [Audits](vaults/audits.md)
