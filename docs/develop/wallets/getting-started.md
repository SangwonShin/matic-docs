# 제목 없음

---

id: getting-started
title: Wallets
sidebar_label: Getting Started
description: Get a list of supported wallets and manage key strategy.
keywords:

- wiki
- polygon
- wallet
- integrate
- guide
image: [https://wiki.polygon.technology/img/polygon-wiki.png](https://wiki.polygon.technology/img/polygon-wiki.png)

---

:::tip Stay in the know

Keep up with the latest Wallet Suite updates from the Polygon team and community by subscribing to our [<ins>Notifications</ins>](https://polygon.technology/notifications/).

:::

Wallets that support Polygon allow for key management, access to accounts controlled by
private keys, and interfaces that allow users to perform chain actions and sign transactions.
The following page serves as a wallet index for wallets compatible with Polygon. Please note
that this is not an exhaustive.

:::caution Third-party wallets

These third-party wallets have integrated Polygon and support a variety of features.
**You should do your own due diligence before using them**. The official Polygon
Support cannot provide assistance for issues with these wallets or other non-native wallets.

:::

:::info Centralized Exchanges (CEXs)

For a list of CEXs that support Polygon, visit a third-party tracking website such as
[<ins>**CoinMarketCap**</ins>](https://coinmarketcap.com/currencies/polygon/markets).

:::

## Native Wallets

[Polygon Support](https://support.polygon.technology/support/home) can provide assistance to users and address issues related to the following wallets:

| Wallet | Custody | Account Type | Multi-Sig | dApp Browser | Platform |
| --- | --- | --- | --- | --- | --- |
| https://wallet.polygon.technology/login/ | non-custodial | EOA | no | no | browser |
| https://wallet.hermez.io/login | non-custodial | EOA | no | no | browser |
| https://devnet-avail.polygon.technology/ | non-custodial | EOA | yes | no | browser |

## Partner Wallets

The following wallets are solutions that Polygon Technology has partnered with:

| Wallet | Custody | Account Type | Multi-Sig | NFT | dApp Browser | Bridge Support | Fiat On-Ramp | Platforms |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| https://1inch.io/wallet/ | non-custodial | EOA | no | interface | yes | yes | yes | mobile |
| https://alphawallet.com/ | non-custodial | EOA | no | interface | yes | yes | yes | mobile, api/sdk |
| https://atomicwallet.io/* | non-custodial | EOA | no | no | no | no | yes | mobile, desktop, api/sdk |
| https://www.ambire.com/ | non-custodial | smart contract | no | interface | no | yes | yes | browser |
| https://bitkeep.com/ | non-custodial | EOA | no | interface | yes | yes | yes | mobile, browser extension |
| https://www.bitski.com/ | custodial | EOA | no | interface | no | yes | no | browser, api/sdk |
| https://coin98.com/wallet | non-custodial | EOA | no | interface | yes | yes | yes | mobile, browser, api/sdk |
| https://www.coinbase.com/wallet | hybrid | EOA | no | interface | yes | yes | yes | mobile, browser, api/sdk |
| https://cypherd.io/ | non-custodial | EOA | no | yes | yes | yes | yes | mobile |
| https://dcentwallet.com/ | hybrid | EOA | no | interface | yes | yes | no | mobile |
| https://www.exodus.com/ | non-custodial | EOA | no | yes | no | no | yes | mobile, desktop |
| https://gnosis-safe.io/ | non-custodial | smart contract | yes | interface | no | no | no | mobile, browser, desktop, api/sdk |
| https://guarda.com/ | non-custodial | EOA | no | no | no | yes | yes | mobile, browser, desktop |
| https://www.itoken.com/en | non-custodial | EOA | no | yes | yes | yes | no | mobile |
| https://www.ledger.com/ | non-custodial | EOA | no | interface | no | no | yes | hardware, mobile, desktop |
| https://loopring.io/#/ | non-custodial | smart contract | no | no | no | no | no | mobile, api/sdk |
| https://fortmatic.com/* | custodial | EOA | no | no | no |  |  | mobile, browser, api/sdk |
| https://mathwallet.org/en-us/ | custodial | EOA | no | no | no | yes | yes | mobile, browser, api/sdk |
| https://metamask.io/* | non-custodial | EOA | no | interface | yes | no | no | mobile, browser, api/sdk |
| https://multis.co/* | non-custodial | EOA | no | no | no |  | yes | mobile, desktop |
| https://www.myetherwallet.com/* | non-custodial | EOA | no | interface | no |  | yes | mobile |
| https://omni.app/ | non-custodial | EOA | no | interface | no | yes |  | mobile, api/sdk |
| https://www.opera.com/crypto/next* | non-custodial | EOA | no | support | yes |  |  | mobile, browser |
| https://www.pillar.fi/ | non-custodial | EOA | no | interface | no |  | yes | mobile |
| https://rainbow.me/ | non-custodial | EOA | no | interface | yes |  | no | mobile, api/sdk |
| https://safepal.io/ | non-custodial | EOA | no | no | yes | yes |  | hardware, mobile, api/sdk |
| https://sequence.app/auth | non-custodial | smart contract | no | interface | no |  |  | browser, api/sdk |
| https://simplehold.io/ | non-custodial | EOA | yes | no | no |  | yes | mobile, api/sdk |
| https://www.tokenpocket.pro/en | non-custodial | EOA | no | support | yes | yes | yes | mobile, browser, api/sdk |
| https://toruswallet.io/ | non-custodial | EOA | yes | support | no | no | no | browser, api/sdk |
| Trezor | non-custodial | EOA | no | support | no |  |  | hardware, mobile |
| https://trustwallet.com/ | non-custodial | EOA | no | support | yes |  | yes | mobile |
| https://unstoppable.money/ | non-custodial | EOA | no | yes | yes |  | no | mobile, api/sdk |
| https://www.venly.io/ | hybrid | smart contract | no | interface | no |  |  | browser, api/sdk |
| https://wirexapp.com/en/wirex-wallet* | non-custodial | EOA | yes | no | no |  |  | mobile |
| https://www.xdefi.io/ | non-custodial | EOA | no | interface | no | no | no | browser |
| https://zerion.io/ | non-custodial | EOA | no | yes | yes | yes |  | mobile, browser |

:::caution Non-native wallet support

Wallets denoted with * in the table above are not natively supported with the wallet software
and require manual steps to add the Polygon network.

:::

## Key Management Strategy

The following basic steps allow for the integration of a client-side application with Polygon:

1. **Set up Web3**: [web3.js](https://web3js.readthedocs.io/) is a javascript library that
allows a client-side application to talk to the blockchain. We configure web3.js to communicate
via a developer-based wallet like MetaMask. Use the [web3.js docs](https://web3js.readthedocs.io/en/v1.2.2/getting-started.html#adding-web3-js) to learn about adding `web3.js` to your project.
2. **Set up an Account**: You will be able to send transactions (specifically those that alter the
state of the blockchain).
3. **Instantiate Contracts**: Once a web3 object is in place, we next instantiate our deployed contract,
with which we interact.
4. **Call functions**: Fetch data via functions in the contract through our contract object.
