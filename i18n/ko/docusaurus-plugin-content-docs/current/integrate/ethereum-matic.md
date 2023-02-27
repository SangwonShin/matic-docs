---
id: ethereum-polygon
title: 이더리움 ↔ Polygon
description: Polygon에서 차세대 블록체인 앱을 구축하세요.
keywords:
  - docs
  - matic
  - polygon
  - ethereum
image: https://wiki.polygon.technology/img/polygon-wiki.png
---

# 이더리움 ↔ Polygon {#ethereum-polygon}

플라즈마 보안 솔루션은 이더리움에서 Polygon으로 또는 그 반대로 여러분의 자산을 이전합니다.
* Polygon 플라즈마 계약과의 상호 작용을 위해 [matic.js](https://github.com/maticnetwork/matic.js)를 사용합니다.

## 흐름 {#flow}
다음은 Polygon에 대한 계약 배포와 이더리움↔Polygon에 대한 지원의 흐름입니다.

1. 사용자는 ERC-20 토큰을 이더리움에  XToken으로 배포합니다.

2. 이제 [Polygon](https://t.me/joinchat/HkoSvlDKW0qKs_kK4Ow0hQ)과 계약 주소를 공유합니다. 다음은 요청 예시입니다.

> 안녕하세요 여러분, 우리는 Polygon에 배포된 AwesomeDAPP입니다. 이더리움에서 Polygon 또는 Polygon에서 이더리움으로 자산을 이전할 솔루션을 찾고 있습니다. <br/><br/>
> 내 AwesomeDAPP에 대한 간단한 설명...<br/><br/>
> Token_Address on Ropsten-> "0x.."<br/>
> Token_Name-> "XToken"<br/>
> Token_Symbol-> "X"<br/>
> Token_Decimals-> "18"<br/><br/>
> Polygon 테스트넷 버전으로 이 토큰을 매핑할 것을 요청합니다.<br/>

우리는 여러분을 위해 차일드 계약을 Polygon에 배포할 예정으로, 이 계약은 요건에 따라 유연하고 토큰 이더리움 ↔ Polygon에 매핑될 수 있습니다. (Polygon에 배포하려면  네이티브 토큰 Polygon이 필요한데, 이는 이더리움에서 Polygon으로 입금되거나 Secondary Market Place에서 구매할 수 있습니다.)

3. 사용자는 Xtokens을 발행하고 이더리움에서 전송할 수 있습니다. 예를 들면, 100XToken이 발행되었고 다른 계정으로 이전한다고 가정해 봅니다.

4. 해당 토큰을 Polygon 체인에서 사용하려면 function deposit을 호출하여 두 트랜잭션을 먼저 승인한 후에 depositERC20을 호출합니다.

5. 이제 100XToken은 동일한 주소의 Polygon 체인에서 사용 가능합니다.

6. 사용자의 주소에서 새로운 주소로 50XToken을 이전할 수 있습니다. Polygon에서의 트랜잭션은 이더리움과 유사하며, Polygon은 자체 네이티브 토큰을 사용합니다.

7. 사용자가 해당 XToken을 이더리움 체인으로 되돌리고 싶다면, StartWithdraw를 호출하여 childTokenContract로 부터 인출하고, 해당 토큰을 Polygon 체인에서 소각합니다. 악의적인 행위를 방지하기 위해 일련의 유효성 검사가 진행됩니다. 완료된 후, 토큰은 이더리움 체인에서 사용할 수 있습니다.

8. 해당 토큰을 사용자의 EOA 또는 계정 주소로 다시 받으려면 processExits()을 호출합니다.

9. 이더리움 메인넷의 사용자의 계정 주소에 50XToken이 있어야 합니다.
