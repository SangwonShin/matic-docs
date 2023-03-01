---
id: ethereum-polygon
title: Ethereum ↔ Polygon
description: Construa sua próxima aplicação de blockchain na Polygon.
keywords:
  - docs
  - matic
  - polygon
  - ethereum
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

# Ethereum ↔ Polygon {#ethereum-polygon}

Solução segura de Plasma para transferir os seus ativos de Ethereum para Polygon e vice-versa.
* Use [matic.js](https://github.com/maticnetwork/matic.js) para interagir com os contratos Plasma da Polygon.

## Fluxo {#flow}
Aqui está o fluxo com a implantação dos seus contratos na Polygon e o suporte para Ethereum↔Polygon.

1. Utilizador implanta token ERC-20 para Ethereum - XToken

2. Agora partilhe o seu endereço de contrato com a [Polygon](https://t.me/joinchat/HkoSvlDKW0qKs_kK4Ow0hQ). Aqui está um exemplo de solicitação...

> Olá a todos, nós somos a AwesomeDApp implantada na Polygon. À procura de uma solução para transferir os meus ativos de Ethereum para Polygon e vice-versa. <br/><br/>
> Uma breve descrição da minha AwesomeDApp...<br/><br/>
> Endereço_Token em Ropsten-> "0x.."<br/>
> Nome_Token-> "XToken"<br/>
> Símbolo_Token-> "X"<br/>
> Token_Decimais-> "18"<br/><br/>
> Solicitação para mapear estes tokens para a versão testnet da Polygon.<br/>

Iremos implantar um contrato filho para si na Polygon que pode ser flexível baseado nos requisitos e mapeado para os seus tokens Ethereum ↔ Polygon.( A implantação na Polygon requer o token Polygon nativo, que pode ser depositado de Ethereum para Polygon ou pode ser comprado no Mercado secundário.)

3. O utilizador pode Mint os Xtokens e transferir na Ethereum. Por exemplo, digamos que 100XToken são Mint e depois transferidos para outra conta.

4. Para dispor destes tokens na chain da Polygon, a função de depósito CALL irá chamar duas transações, primeiro aprovar e depois depositar ERC20.

5. Agora 100XTokens estão disponíveis no mesmo endereço da chain da Polygon.

6. Pode transferir 50 XToken do SeuEndereço para NovoEndereço. Novamente, para transações na Polygon semelhante a Ethereum, a Polygon usa o seu próprio token nativo.

7. Se os utilizadores quiserem voltar a receber estes Xtoken na chain Ethereum então CALL IniciarRetirada que irá retirar do ContratoTokenFilho e queimar estes tokens na chain da Polygon. Para evitar qualquer má participação, terá lugar um conjunto de validação. Uma vez feito, os tokens estarão disponíveis na chain Ethereum.

8. CALL processExits() para receber esses tokens de volta para EOA ou no seu endereço da conta.

9. Deverá ver 50 XToken na mainnet Ethereum no seu endereço da conta.
