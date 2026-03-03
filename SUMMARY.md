# Table of contents

* [Introduction](README.md)

## Learn

* [How to use Hako](learn/quickstart.md)
* [Deposit](learn/deposit.md)
* [Withdrawal](learn/withdrawal.md)

## Integration

* [Getting Starting](integration/getting-starting.md)
* [API](integration/api/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: true
      downloadLink: true
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: hako-api
    ```

## Contracts

* [Stable Vault](contracts/stable-vault.md)
* [Stable Gateway](contracts/stable-gateway.md)
* [Addresses](contracts/addresses.md)

## Architecture

* [Vault Allocations](architecture/vault-allocations.md)
* [Withdrawal Queue](architecture/withdrawal-queue.md)
* [Risks](architecture/risks.md)
* [Roadmap](architecture/roadmap.md)
