---
id: withdraw-start
title: withdrawStart
keywords:
- 'plasma client, erc721, withdrawStart, polygon, sdk'
description: 'Memulai dengan maticjs'
---

# withdrawStart {#withdrawstart}

Metode `withdrawStart` dapat digunakan untuk memulai proses penarikan yang akan membakar token yang ditentukan pada rantai polygon.

```
const erc721Token = plasmaClient.erc721(<token address>);

const result = await erc721Token.withdrawStart(<token id>);

const txHash = await result.getTransactionHash();

const txReceipt = await result.getReceipt();

```