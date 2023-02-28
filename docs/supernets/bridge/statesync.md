---
id: statesync
title: StateSync
description: ""
keywords:
  - docs
  - polygon
  - edge
  - bridge
  - fxportal
  - childchain
  - rootchain
  - statesync
---

A Polygon Edge-based chain, that is, a `childchain`, can connect to a layer 1 network,
that is, a `rootchain` to send and receive messages.

A user sends a transaction into a [`StateSender`](../contracts/state-sender.md) contract.
A [`StateReceiver`](/supernets/advanced/contracts/state-receiver.md) contract is present on the `childchain`
that is used to listen for state `data`.

![Commitment Diagram](/img/supernets/commitment.png)

The `childchain` calls the `StateSync` method to synchronize the states. Once the length
of the data that the `rootchain` reaches **2048**, that is, a predefined number of `StateSync`t
events in the `rootchain`, the `childchain` will create a commitment using a merkle trie
for the `StateSync` objects.

The `root` of the Merkle trie is the *commitment root*. Edge uses the **memory pool** to
gather async signatures from validators for the proposed commitment root. Once a super-majority
of validators have provided signatures, the Edge node can submit the `commit` into a new
block, and the commitment root is recorded.

To execute a `StateSync` object, a user can query the Edge node for a specific `commit`
leaf in the Merkle trie, where the Edge node will return a proof of the Merkle trie, and
the user can send a `withdraw` operation with the proof. The withdraw operation is what
executes the `StateSync`.
> `withdraw` does not have to be called from the client
> and can be called arbitrarily so long as proper the right `commit` and proof
> are supplied.

![Execution Diagram](/img/supernets/withdraw.png)

:::note Comparing execution to PoS v1

In Polygon PoS v1, the execution of the commitments were done upon adding the commitment
into a new block. By adding an intermediary step that separates the *committing* from
*execution*, the network health improves, and the chain can prevent potential DDoS attacks as
each execution requires a gas fee. This also encourages optimized use of the bridge.

:::

Here is the typical process of how the `StateReceiver` contract might be used to commit
and execute a group of StateSync objects:

- The system address creates a `StateSyncCommitment` representing a group of state sync events
  and signs.
- The system address calls the commit function with the `StateSyncCommitment`, the `signature`,
  and the `bitmap` as arguments.
- The `commit` function verifies the signature and stores the `StateSyncCommitment` in the
  `commitments` mapping.
- A client calls the `execute` function with the `proof` and the `StateSync`.
- The `execute` function verifies the `proof` and processes the state sync event.
