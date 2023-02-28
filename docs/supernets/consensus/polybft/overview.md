---
id: polybft-overview
title: Polygon Byzantine Fault Tolerance (PolyBFT)
sidebar_label: What is PolyBFT
description: "Explanation about PolyBFT, the consensus mechanism for Polygon Edge."
keywords:
  - docs
  - polygon
  - edge
  - consensus
  - polybft
  - pos
---

## Overview

PolyBFT is a sophisticated consensus mechanism used by Supernets that employs two integral components: a **consensus engine** and a **consensus protocol**. The [IBFT 2.0 protocol](https://github.com/0xPolygon/go-ibft) is leveraged as the consensus engine to seal blocks, while a staking solution for the protocol is implemented through [system smart contracts](https://github.com/0xPolygon/core-contracts).

<div id="my-element">Hover over me to see a tooltip!</div>

<!-- TO ADD PROPER DIAGRAM -->

```plaintext
                            +----------------------------+
                            |           PolyBFT          |
                            |          Consensus         |
                            +----------------------------+
                                            |
                                            |
                            --------------------------------
                            |                              |
                            |                              |
              +--------------------------+    +--------------------------+
              |        IBFT 2.0          |    |          Core            |
              |        Consensus         |    |          Smart           |
              |         Engine           |    |         Contracts        |
              +--------------------------+    +--------------------------+
```

The consensus engine itself is based on the popular Practical Byzantine Fault Tolerance (PBFT) consensus, with a particular adaptation for PolyBFT called the Istanbul Byzantine Fault Tolerance (IBFT). As a Byzantine Fault Tolerance network, PolyBFT is capable of maintaining network integrity even in the presence of malicious or dishonest nodes. This fault tolerance is achieved by allowing up to `f` faulty nodes in a network of `3f + 1` nodes, as long as two-thirds of the nodes are honest. This is also known as a "super-majority rules" algorithm.

Each PolyBFT node keeps a local copy of the blockchain, which is represented as a list of blocks similar to the Ethereum blockchain. The height of a block is defined as the number of parent links that separate the block from the genesis block, with the genesis block having a height of 0. Sequential instances of a block finalization protocol are run, with the objective of each instance being to determine which Ethereum block is to be added at height h of the blockchain.

<!-- ANOTHER DIAGRAM -->

```plaintext
                            +----------------------------+
                            |           PolyBFT          |
                            |          Consensus         |
                            +----------------------------+
                                            |
                                            |
                            --------------------------------
                            |                              |
                            |                              |
              +--------------------------+    +--------------------------+
              |        IBFT 2.0          |    |          Core            |
              |        Consensus         |    |          Smart           |
              |         Engine           |    |         Contracts        |
              +--------------------------+    +--------------------------+
```

## Consensus Engine: IBFT 2.0

Specifically, Polygon Edge uses [IBFT 2.0](https://github.com/0xPolygon/go-ibft) as a consensus
engine to seal blocks.

## Consensus protocol: PolyBFT

The consensus protocol uses the IBFT consensus engine and proof-of-stake architecture to seal blocks,
provide specific network capabilities and govern the network. The consensus engine works with a set of
core smart contracts that implements a staking solution and incentivization scheme which defines all the
network's proof-of-stake rules.

Polygon Edge follows [delegated proof of stake consensus](../../maintain/delegate/delegate.md), where
delegators delegate their MATIC to back validators on the network.

The consensus protocol follows a set of state transitions. While things are still being finalized, the
process will typically follow the steps below.

1. A validator proposes a new block to be added to Polygon. This block contains a list of transactions
   that the validator would like to include in the next update to the blockchain's state.

2. Other validators in the active set will vote on whether to accept the proposed block. A
   certain number of validators must agree to accept the block to reach consensus. The voting weight of
   each validator influences voting. The protocol refers to block height as a *sequence*.

    The process to finalize a block in PolyBFT is known as *sealing*. The sealing of blocks is instant
    and final. All nodes in the network exchange information for a given sequence.

    When a validator proposes a new block, other validators on the network will vote on whether to
    accept the block. This process is typically repeated several times; each repetition is known as a
    *round*. During each round, a certain number of validators must agree to seal the proposed block
    for it to be added to the blockchain. If the required number of votes is not reached during a
    particular round, the voting process will continue into the next round, and thus, the protocol
    "increases the round". Another validator will attempt to seal the sequence in the new round.
    > The best case for a proposed block is that it is sealed at round 0. Blocks that are repeatedly
    > sealed at a high-order round which usually indicates a problem with the network.

3. If the proposed block is accepted, it will be added to the blockchain, and the state of the blockchain
   will be updated to reflect the changes introduced by the transactions in the block.
   > If a malicious actor attempted to fork the network, they would need to obtain control of 2/3 of
   > the network, which PolyBFT prevents.

4. Once the state of the blockchain has been updated, the next proposer will propose a new block, and
   the process repeats.

IBFT limits network participation to around 100 validators. A variable amount of stake is used as a fixed
stake criterion to limit the system's security and can make the system economically vulnerable. The
validator set in the PolyBFT does not update on each block but is fixed during  `n` block periods known as
an `epoch`.

> The `n` block period to define one epoch is to be determined by governance. Until then, validators will
> remain the same. At the end of the epoch, a special `state transaction` to `validatorSetManagementContract`
> is emitted, notifying the system about validators’ uptime during the `epoch`. It is up to the smart contract
> to reward validators by their uptime and **update the validator set** for the next `epoch`. There is a
> function `getValidatorSet` which returns the current validator set at any time.

Staking is governed by staking contracts directly on Polygon. To be clear, the staking module validates on
Polygon and does not rely on Ethereum's security, but in principle, two chains are securing the network, PoS
client and Ethereum. Transaction checkpoints still occur on Ethereum, but Ethereum does not validate staking
on Polygon.

:::note

Note that in Tendermint, an epoch is set to 1. However, PolyBFT includes the logic to set a custom
epoch time, with the intent of each epoch being one day in blocks, or around 14000 blocks.

:::

A reward calculation occurs at the end of the epoch to reward the active validators in that epoch.

:::caution Slashing

Like in other proof-of-stake systems, validators are subject to slashing for malicious activity or
poor performance. The slashing mechanics are still being determined, but PolyBFT will undoubtedly
include a mechanism to penalize bad actors. Slashing a validator typically involves a penalty, such
as losing some or all of their stake on the network.

Examples of malicious activities are double-signing and equivocation:

- Double-signing refers to the act of signing two conflicting transactions. When a validator double-signs,
  it creates a situation where the network is unable to reach consensus on the state of the blockchain,
  which can lead to problems such as an attempt to fork or network instability.

- Equivocation refers to the act of a validator attempting to create two conflicting versions of the
  blockchain, which can also lead to problems such as fork or network instability.

:::

## Optional In-built bridge integration

With the help of PolyBFT, the Polygon client supports an
[in-built bridging mechanism (a two-way bridge)](/supernets/bridge/overview.md),
which enables arbitrary message passing between a Supernet (`childchain`) and another proof-of-stake
blockchain (`rootchain`). Transfers can occur without mapping.

Learn more [here](/supernets/bridge/overview.md).
