---
id: polybft-selection
title: Validator Selection
sidebar_label: Selection
description: "Explanation about PolyBFT, the consensus mechanism for Polygon Edge."
keywords:
  - docs
  - polygon
  - edge
  - consensus
  - polybft
  - pos
---


The current implementation of the consensus engine only allows for whitelisting of validators.

To add a new validator, an authorized address must call the whitelistValidator function and provide the validator's address, bls public key, and staking amount. When a validator is whitelisted, it is added to the tree data structure (in _validators) and becomes eligible to participate in consensus rounds.

The sortedValidators function is used to retrieve the list of currently active validators, sorted by their stake. This function uses the ValidatorTree library, which is implemented in ValidatorStorage.sol, to retrieve and sort the list of validators.

Here's a summary of how the whitelist functionality works:

An authorized address calls the whitelistValidator function and provides the validator's address, bls public key, and staking amount.
The function checks that the staking amount is at least equal to the minimum required staking amount, and that the validator address has not already been added to the tree.
If these checks pass, the function creates a Validator struct with the provided data and inserts it into the _validators tree using the insert function provided by the ValidatorTree library.
The function emits a ValidatorWhitelisted event with the validator's address and staking amount as arguments.
