---
id: ethereum-polygon
title: Ethereum ↔ Polygon
description: Создайте следующее блокчейн-приложение на Polygon.
keywords:
  - docs
  - matic
  - polygon
  - ethereum
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

# Ethereum ↔ Polygon {#ethereum-polygon}

Решение на основе модели безопасности Plasma для перевода ваших активов между Ethereum и Polygon.
* Используйте [matic.js](https://github.com/maticnetwork/matic.js) для взаимодействия с контрактами Plasma Polygon.

## Процесс {#flow}
Процесс развертывания контрактов на Polygon и поддержки для переводов между Ethereum↔Polygon следующий:

1. Пользователь развертывает токен ERC-20 в Ethereum — XToken.

2. Теперь необходимо сообщить [Polygon](https://t.me/joinchat/HkoSvlDKW0qKs_kK4Ow0hQ) адрес контракта. Приведем пример запроса…

> Всем привет! Мы разработчики приложения AwesomeDApp, развернутого на Polygon. Нам нужно решение для перевода активов из Ethereum в Polygon и обратно. <br/><br/>
> Краткое описание AwesomeDApp…<br/><br/>
> Token_Address на Ropsten-> "0x.."<br/>
> Token_Name-> "XToken"<br/>
> Token_Symbol-> "X"<br/>
> Token_Decimals-> "18"<br/><br/>
> Просьба сопоставить эти токены с тестовой версией сети Polygon.<br/>

Мы развернем для вас дочерний контракт в Polygon, который может быть гибким в зависимости от соответствующих требований и может быть сопоставлен с вашими токенами Ethereum ↔ Polygon. Для развертывания в Polygon требуется нативный токен Polygon, который можно перевести из Ethereum в Polygon или приобрести на вторичной торговой площадке.

3. Пользователь может создавать токены Xtoken и переводить их в Ethereum. Так, предположим, что было создано 100 XToken, которые затем были переведены на другой аккаунт.

4. Чтобы получить эти токены в Polygon Chain, используйте вызов функцию deposit (внести), которая вызовет две транзакции: сначала approve (утвердить), а затем depositERC20 (внести ERC20).

5. Теперь 100 XToken доступны в цепочке Polygon Chain по тому же адресу.

6. Вы можете перевести 50 XToken с YourAddress (ваш адрес) на NewAddress (новый адрес). Опять же, для выполнения транзакций в Polygon, аналогичных транзакциям в Ethereum, Polygon использует собственный нативный токен.

7. При желании вернуть Xtoken в цепочку Ethereum пользователи могут вызвать функцию StartWithdraw (начать вывод средств). Она позволит вывести токены из childTokenContract (контракт дочерних токенов) и сжечь их в Polygon Chain. Во избежание недобросовестного участия будет выполнен ряд проверок. После этого токены будут доступны в цепочке Ethereum.

8. Выполните функцию processExits(), чтобы получить эти токены обратно на адрес своего EOA (внешнего аккаунта) или адрес своего аккаунта.

9. На адресе вашего аккаунта в Ethereum mainnet появится 50 XToken.
