---
id: overview
title: Architecture Overview
sidebar_label: Overview
description: Introduction to the architecture of Polygon Supernets.
keywords:
  - docs
  - polygon
  - edge
  - architecture
  - modular
  - layer
  - libp2p
  - extensible
---

:::caution Active development

The Supernets documentation is a work in progress. Currently, this Hub only extends
the original **[<ins>Polygon Edge</ins>](/edge/overview.md)** documentation to provide material for
the functionality in the
**[<ins>v0.7.0-alpha release</ins>](https://github.com/0xPolygon/polygon-edge/releases/tag/v0.7.0-alpha1)**.

**The code is undergoing an audit and should not yet be used in production environments.**
Please get in touch with the Polygon team if you would like to use it in production or have any questions.

:::

The following diagram offers an architectural overview for Polygon Supernets.

![Polygon Supernets Architecture](/img/supernets/architecture.png)

## libp2p

**[libp2p](https://libp2p.io/)** serves as the networking layer for Supernets.
Libp2p fits with the designing philosophies of Supernets as it is designed for
peer-to-peer network architectures while being modular, extensible, and fast. Most
importantly, it provides a great foundation for more advanced features, which we'll
cover later on.

## Bridge

With the help of PolyBFT, Polygon Edge supports an in-built bridging mechanism
(a two-way bridge), which enables arbitrary message passing between
a Supernet (`childchain`) and another proof-of-stake blockchain (`rootchain`). Transfers can
occur without mapping.

Message passing between a `rootchain` to a given `childchain` is
accomplished by continuously syncing the state of the `rootchain` with the `childchain`, known
as `StateSync`. These transfers of state happen between system calls. The mechanics of `StateSync`
are explained [here](bridge/statesync.md).

When passing messages from a `childchain` to a `rootchain`, the validator set will commit
`checkpoints`, a snapshot of the `childchain` state. The mechanics of `checkpoints` are explained
[here](bridge/checkpoint.md).

## Message Pool

The Message Pool is a means that allows multiple validators to aggregate their signatures
to create a single, aggregated signature that represents the signatures of all validators in
the pool. The pool is used as a way to get an asynchronous agreement for some data for the
`StateSyncs` as each node might have a different event state from the `rootchain`, a process
that is not synchronous.

The pool is implemented using a variant of the BLS signature scheme. Validators must join
a Message Pool and agree to follow a set of rules and procedures for generating and aggregating
their signatures. Once a group of validators has joined a Message Pool, they can use the Message
Pool feature to create aggregated signatures on messages or transactions, which can then be
verified by the network using the Message Pool's public key.

## Consensus

PolyBFT is the consensus mechanism of Polygon Edge. It is composed of two
core parts, a consensus engine, PBFT, and a consensus protocol, which includes the
[bridge](#bridge), staking, and other utilities. PolyBFT uses an adaptation of
PBFT (Practical Byzantine Fault Tolerance) consensus, known as IBFT (Istanbul Byzantine
Fault Tolerance), in combination with the Tendermint-based re-locking mechanism. More is
explained in the [PolyBFT overview](polybft.md).

## Blockchain

The Blockchain layer is the central layer that coordinates everything in the Polygon Edge
system. The blockchain curates state transitions and is responsible for state changes when
a new block is added to the chain. It is covered in depth in the corresponding
[Module documents](../edge/architecture/modules/state.md).

## Runtime (EVM)

Supernets use the EVM as their runtime environment for executing smart contracts.
The runtime environment is designed to be highly scalable and fast, with the ability
to process transactions and execute smart contracts at speeds that are significantly
faster than those of the Ethereum mainnet.

## TxPool

The TxPool layer represents the transaction pool, and it is closely linked with other modules
in the system, as transactions can be added from multiple entry points.

## JSON RPC

The JSON RPC layer is an API layer that dApp developers use to interact with the blockchain.
It is covered in depth in the [Module documents](/edge/architecture/modules/json-rpc.md).

## gRPC

The gRPC layer is vital for operator interactions. Through it, node operators can
easily interact with the client, providing an enjoyable UX.

The gRPC layer helps abstract all the request/reply protocols and simplifies the streaming
protocols needed for the client to function. Protocol buffers are used to define services
and message structures. It is covered in depth in the
[Module documents](/edge/architecture/modules/networking.md).
