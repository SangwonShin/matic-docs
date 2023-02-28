---
id: ibft
title: IBFT 2.0
description: "Explanation about PolyBFT, the consensus mechanism for Polygon Edge."
keywords:
  - docs
  - polygon
  - edge
  - consensus
  - polybft
  - pos
---

## Consensus Engine

IBFT includes a validator pool (or set) responsible for validating candidate blocks proposed
by a randomly selected block proposer who is part of the validator pool. The proposer is responsible
for constructing a block at the block interval. The proposer mechanism is based on Tendermint, where
a proposer is chosen based on a deterministic selection algorithm. The frequency in selection is
proportional to the voting power of the validator.

 > The amount of voting power a validator has is proportional to the amount of stake that they have locked
 > up on the Polygon PoS network. This means that validators with more stake will have more voting power and, therefore,
 > more influence over the decision-making process on the network.

In general, each block in IBFT requires at least one round of voting
by the validator to arrive at consensus, which is recorded as a collection of signatures on the
block content. Only when there is no consensus on a given block, multiple rounds are needed.

> The ideal path would be when the validator pool reaches consensus on a candidate block
> in the first round of voting, and the block is added to the blockchain without the need
> for additional rounds of voting. This is the most efficient and optimal outcome, as it
> allows the network to continue processing transactions and adding new blocks to the chain
> in a timely manner.

A super-majority of validators must confirm that block is valid in order for the
block to be added to the blockchain.

:::note

The proposer selection algorithm still needs to be determined. It will resemble the diagram,
where x, y, z are input parameters related to the selection, the "Round #" is the current
Round Number of the system, and "validator n" is the selected proposer.

<!-- TO ADD PROPER DIAGRAM -->

```plaintext
       __________          __________          __________       __________
      |          |        |          |        |          |     |          |
      |    x     |        |    y     |        |    z     |     | Round #  |
      |__________|        |__________|        |__________|     |__________|
          |                 |                    |                 |
          |                 |                    |                 |
          |                 |                    |                 |
       _______________________________________________________________________
      |                                                                       |
      |                validator proposer selection algorithm                 |
      |_______________________________________________________________________|
                                          |
                                          |
                               _______________________
                              |                       |
                              |      validator n      |
                              |_______________________|
```

:::

### Benefits of IBFT 2.0

- **Immediate block finality**: Only one block is proposed at a given chain height. Thus, the
  single chain removes forking,
  uncle blocks, and the risk that a transaction may be “undone” once on the chain later.
- **The reduced time between blocks**: The effort needed to construct and validate blocks is
  decreased significantly and increases the chain's throughput.
- **High data integrity and fault tolerance**: IBFT uses a pool of validators to ensure the
  integrity of each proposed block. A super-majority (~66%) of these validators are required to
  sign the block before insertion to the chain, making block forgery very difficult. Also, the
  proposer of the block rotates over time — ensuring a faulty node cannot exert long-term influence
  over the chain.
- **Operationally flexible**: The validators can be modified in time, ensuring the group contains
  only full-trusted nodes.

### Benefits

- **Liveness**: It has been proven that IBFT does not guarantee BFT persistence nor liveness
  when operating on a synchronous network. If a validator receives enough confirmation about a block,
  it can lock the proposed block (assuming it has not locked any prior). If a change were to occur
  because of a fault in the network, it could trigger the activation of the round change protocol,
  where the protocol would expect to commit the locked block at that specific height.

- **Persistence**: If, for instance, there is a faulty network condition present where two different
  node subsets lock to two different blocks, the system enters into an infinite cycle of state transitions
  that cannot converge states and finalize the block.