---
eip: <to be assigned>
title: Transaction bundles
description: Allowing users to submit atomic transaction bundles.
author: Théo Szymkowiak (@Theo-)
discussions-to: <URL>
status: Draft
type: Standards
category (*only required for Standards Track): Core
created: 2022-07-11
requires (*optional): None
---

## Abstract
Transaction bundles are a way to specify a sequence of transactions that need to be executed in the same block, in a specific order, without any other transactions in-between.

## Motivation
Currently, transactions are submitted one by one. The only way to execute multiple operations at once is to deploy a multicall contract. But multicall contracts have a few drawbacks:

- Only one transaction sender.
- It requires users to deploy a contract, which consumes blockchain disk space, which is redundant.
- It requires users to issue authorizations to the multicall contract to perform operations with their funds.

Transaction bundles would significantly simplify the user experience for many dApps. Users would not need to perform a series of transactions, and wait for them to be mined, before beings able to use a protocol. For example: approve the protocol of a token, then use the said protocol.

Those bundles woud allow users to submit transactions that are "bundled" together and need to be executed atomatically.

## Specification
The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

We propose to add two fields to transaction, thus creating a new transaction type (`3`). A `next` field that points to the hash of the next transaction in a bundle. And an `index` field that marks the position of a transaction in a bundle.

The `index` field is important in case a miner receives a part of the bundle before receiving the first transaction. It allow the miner to tell that a transaction is the first of a bundle.

If any transaction of a bundle reverts, all transaction needs to revert. (not sure about this).

## Rationale
 We could use `previous` field instead of an `index` field, but it would consume more space. A previous field would be 32 bits while the index field would be 3 bits. We propose that bundles are limited to `2^3 = 8` transactions (and maximum 50% of a block space) to prevent them from consuming a lot of block space.

## Backwards Compatibility
All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
TODO

## Reference Implementation
TODO

## Security Considerations
This EIP could be used to increase the blast radius of phising scams. One way to prevent this, is to ask wallets to update their UIs for transaction bundles.

## Copyright
Copyright and related rights waived via [CC0](../LICENSE.md).
