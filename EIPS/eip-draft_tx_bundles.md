---
eip: <to be assigned>
title: Transaction bundles
description: Allowing users to submit atomic transaction bundles.
author: Théo Szymkowiak (@Theo-)
discussions-to: <URL>
status: Draft
type: Standards
category: Core
created: 2022-07-11
requires: None
---

## Abstract
Transaction bundles are a way to specify a sequence of transactions that need to be executed in the same block, in a specific order, without any other transactions in-between.

## Motivation
Currently, transactions are submitted one by one. The only way to execute multiple operations at once is to deploy a multicall contract. But multicall contracts have a few drawbacks:

- Only one transaction sender.
- It requires users to deploy a contract, which consumes blockchain disk space, which is redundant. 
- It requires users to issue authorizations to the multicall contract to perform operations with their funds.
- It requires users to have knowledge about how the blockchain works in order to use them effectively.

Transaction bundles would significantly simplify the user experience for many dApps. Users would not need to perform a series of transactions, and wait for them to be mined (which takes 12 seconds each time), before beings able to use a protocol. For example: approve the protocol of a token, wait for a block, then use the said protocol.

Those bundles woud allow users to submit transactions that are "bundled" together and need to be executed atomatically.

## Specification
The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

### Parameters
- `FORK_BLKNUM` = `TBD`
- `CHAIN_ID` = `TBD`
- `TX_TYPE` = TBD, > 0x02 ([EIP-1559](./eip-1959.md))

As of `FORK_BLOCK_NUMBER`, a new [EIP-TDB](./eip-tdb.md) transaction is introduced with `TransactionType` = `TX_TYPE(TBD)`.

The intrinsic cost of the new transaction is inherited from [EIP-2930](./eip-2930.md), specifically `21000 + 16 * non-zero calldata bytes + 4 * zero calldata bytes + 1900 * access list storage key count + 2400 * access list address count`.

The [EIP-TDB](./eip-TDB.md) `TransactionPayload` for this transaction is 

```
rlp([chain_id, next, index, nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, destination, amount, data, access_list, signature_y_parity, signature_r, signature_s])
```

We propose to add two fields to transaction, thus creating a new transaction type (`3`) with an expended transaction envelope. A `next` field that points to the hash of the next transaction in a bundle. And an `index` field that marks the position of a transaction in a bundle.

The definitions of all other fields share the same meaning with [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559)


## Rationale
 We could use `previous` field instead of an `index` field, but it would consume more space. A `previous` field would be `32` bits while the `index` field would be 3 bits. We propose that bundles are limited to `2^3 = 8` transactions (and maximum `50%` of a block space) to prevent them from consuming too much space.

 Another way is to mark the first transaction of the bundle is with a boolean flag `isBundleHead`. This would only consume `1` bit.

 The `index` field is important in case a miner receives a part of the bundle before receiving the first transaction. It allow the miner to tell that a transaction is the first of a bundle.

## Backwards Compatibility

Nodes that havn't upgraded will not be able to process this new transaction type and new transaction envelope. However, it shoulnd't impact non-ugpraded nodes as they will simply ignore the new transaction types.

## Test Cases

## Reference Implementation

## Security Considerations
This EIP could be used to increase the blast radius of phising scams. One way to prevent this, is to ask wallets to update their UIs for transaction bundles.

## Copyright
Copyright and related rights waived via [CC0](../LICENSE.md).
