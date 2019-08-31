# 스마트 컨트랙트가 갖는 취약점

2019 한국전자통신연구원
하계연수생 김승수
nablekim94@gmail.com
(2019년 08월 16일 작성)

<br><br><br>

># Introduction

 본 글은 영국 보안컨설팅 업체 NCC Group가 스마트 컨트랙트 취약점을 조사하기 위해 진행한 **'DASP(Decentralized Application Security Project)'** 의 내용을 다룬다. 자세한 사항은 학습 사이트 Link에 첨부된 dasp 공식홈페이지를 참고하자.

<br><br>

># Index

[TOC]

<br><br>

#### 학습 사이트 Link
---
- DASP(Decentralized Application Security Project) : https://dasp.co/ (10개의 대표적인 문제 by NCC Group 영국 보안컨설팅 업체)
- 한글 번역 : https://allblackk.tistory.com/357
- youtube 강좌 : https://www.youtube.com/watch?v=KMaURu9FC-U (코인사이트)

<br><br>

#### 1. Reentrancy (재귀 호출 취약점)
---
- DAO 피해 사건 : Withdraw(인출) 함수와 Fallback 함수을 재귀적으로 호출하여 자금을 탈취한 문제
  - Ehtereum Classic 과 Ethereum으로 분리된 하드 포크의 계기
  - 해커에게 360만 Ether 탈취됨
  - Code
    1. Victim 컨트랙트의 withdraw() 함수는 자신을 호출한 계정의 잔고를 확인 후, 잔고가 충분하다면 호출한 계정 주소로 이더를 송금한다.
    2. 8번째 줄에 "msg.sender"가 사용자 계정 주소가 아닌 **컨트랙트 계정 주소라면** 이더를 송금받은 컨트랙트는 fallback 함수를 호출하게 된다.
    3. Attacker 컨트랙트의 fallback 함수의 내용에는 victim 컨트랙트의 withdraw()를 또 다시 호출하는 로직이 포함되어 있다.
    4. 또 다시 Victim의 withdraw() 함수가 호출되어 이더가 Attacker 계정으로 송금된다.
    5. 이 과정이 반복 재귀적으로 발생하여 이더가 여러번 송금되어 자금이 탈취되었다.

    ![weakness1](./weakness1.png)

<br><br>

#### 2. Access Control (접근 제어 취약점)
---
***(잘 이해가 안됨)***
- Parity Multisig Wallet 사건
  - 컨트랙트의 소유권을 지정하는 코드가 누구나 실행할 수 있도록 작성된 것이 원인 >> 따라서 external, internal, public 등의 접근 제어자 사용을 적절히 해야함
  - Parity Multisig Wallet은 입출금 같은 핵심 기능들이 구현된 WalletLibrary 컨트랙트와 고객의 지갑 Wallet 컨트랙트로 이루어져 있었음. WalletLibrary 컨트랙트를 배포할 당시, 패러티 개발자는 이를 라이브러리 컨트랙트로 쓸 생각으로 owner 초기화를 하지 않았음. 어떤 한 사용자가 WalletLibrary 컨트랙트의 initWallet 함수를 실행시켰더니 자신이 WalletLibrary 컨트랙트의 owner가 되어버렸고 kill 함수를 호출하여 컨트랙트를 삭제하게 됨. 이로인해 기존에 있던 50만 이더가 동결되는 사태를 초래하였음. [참고](https://steemit.com/kr/@kanghamin/parity)

  ***cf. Multisig Wallet이란?
  트랜잭션 실행 전, 여러 사람들에게 트랜잭션 실행에 대한 동의 여부를 받고 미리 설정한 최소 동의수를 넘으면 트랜잭션을 실행시키는 것. 이더리움 상에서 smart contract를 통해 구현되며 Gnosis와 Parity 사가 대표사이다.***

<br><br>

#### 3. Arithmetic issues (연산 문제)
---
- Overflow, Underflow로 인해 잘못된 결과를 제공하여 프로그램의 안정성과 보안을 손상시킬 수 있는 문제
- Code
  - balances[msg.sender]가 \_amount보다 작은 경우, 둘의 뺏셈 연산은 음수가 나와야하지만 uint타입이라 underflow가 발생하여 엄청 큰 값이 발생한다. 이로 인해, 실제 계정이 갖고 있던 잔고보다 많은 금액이 인출되는 문제가 발생할 수있다.

  ![weakness3](./weakness3.png)

- 이 문제를 해결하기 위해, Open Zeppelin(오픈 제플린) 회사에서 제공하는 [SafeMath 라이브러리][10]를 사용하여 overflow와 underflow를 방지할 수 있다.

<br><br>

#### 4. Unchecked Return Values for Low Level Calls
---
- Silent Failing Sends, Unchecked-Sned라 불리기도 함
- 저수준의 호출의 결과를 적절히 다루지 못한다면 예상치 못한 문제가 발생할 수 있음
- Code
  - msg.sender가 악의적인 Contract 계정이고 이 계약 안에 fallback 함수를 구현하지 않았다고 가정해보자
  - msg.sender.send(\_amount)를 실행했을 때, 이더 수신 측인 악의적인 Contract는 fallback 함수를 구현하지 않았기 때문에 이더를 송금받지 못한다.
  - 이렇게 해서 보내진 이더는 동결되는 상황이 발생하고 이더 송금이 실패했으므로 .send()의 결과 false가 리턴된다.
  - 하지만, 아무런 예외처리가 되어있지 않기때문에 잔고만 줄고 송금된 이더는 동결되는 문제로 끝나버린다.
  - 이 문제를 해결하기 위해 .send() 의 결과에 따른 조치를 취하도록 if 조건문을 넣던지, 아니면 예외 발생시키고 원래 상태로 되돌리는 .transfer()함수 사용을 권장한다.

  ![weakness4](./weakness4.png)

***cf. 저수준 호출이란?
 EVM에서 이해할 수 있는 저수준의 명령어를 Opcode라 한다. Solidity에서는 Opcode를 바로 실행할 수 있는 함수들이 존재하는데 이 함수들을 사용하는 것을 저수준 호출이라 한다.***

<br><br>

#### 5. Denial of Service(DoS)
---
- Gas Limit이 넘도록 함수(반복문이 포함된)를 실행시키거나 Unexpected throw를 실행시키는 등의 악의적인 행동으로 컨트랙트가 제 기능을 못하게 하여 사용할 수 없는 컨트랙트가 되어버리는 문제
(사용할 수 없는 컨트랙트의 의미는 한번 배포한 컨트랙트는 수정이 불가능하므로 수정된 컨트랙트를 아예 새롭게 재배포해야 되므로 기존 컨트랙트는 못쓰게 된다는 뜻)
- Code1
  - 이전 President에 price 금액보다 많은 뇌물을 지급하면 President를 물려받는 컨트랙트 함수가 있다고 가정해보자
  - 이전 President가 악의적인 컨트랙트이고 이 컨트랙트의 fallback 함수에는 revert() 예외 발생 구문이 포함되어 있다고 가정해보자
  - 자신이 다음 President가 되기 위해 이전 President에게 뇌물을 송금했을 때, 이전 President는 독재하기 위해 fallback함수의 revert()를 실행시키므로 현재 트랜잭션은 무효 처리가 된다.
  - 그렇다면 이 함수는 독재가 발생하는 문제가 있기 때문에 수정을 해야하므로 더 이상 이 컨트랙트는 쓸모가 없어진다.
  - 따라서, 돈을 송금할 때 수신측의 계정이 컨트랙트 주소인지 사용자 주소인지 필터링을 하는 과정이 필요하다.
  ![weakness5-1](./weakness5-1.png)

- Code2
  - 사용자들에게 임금을 주는 함수가 있다고 가정해보자
  - 해커가 악의적인 목적으로 사용자 수를 어마어마하게 늘리는 공격을 했다.
  - 사용자에게 임금을 나누어주는 함수를 실행시켜려고 보니 필요한 가스 비용이 Gaslimit을 초과하여 실행할 수가 없는 상황이 벌어진다.
  - 더 이상, 이 컨트랙트 함수를 사용할 수 없다.
  ![weakness5-2](./weakness5-2.png)

<br><br>

#### 6. Bad Randomness
---
- Nothing is Secret이라고도 함
- 블록체인의 투명성으로 드러나거나 예측 가능하다는 등 임의성을 보장받지 못하는 취약점이 있음
- 이더리움에는 **random 함수라는 것 없음**
- 요즘 OS에서는 난수를 뽑아내기 위해서 하드웨어적인 요소를 사용
 ex) 마우스 움직임, CPU 클럭 속도 등을 이용
- But, 블록체인은 분산 시스템이므로 하드웨어적인 요소를 사용해서 난수를 생성한다면 각 노드가 Contract 내에서 생성한 랜덤값이 다를 수 있음
- 그래서 하드웨어적인 요소 대신에 **블록번호, 타임스탬프, 해쉬값을 시드로 사용하는데 이는 <U>취약점이 존재</U>**
 (시드값을 알면 나중에 발생할 랜덤값을 예측할 수 있음)
- Code
  - play() 함수에서 승리 여부를 판단하기 위해 현재 block number의 blockhash 값이 짝수인지 홀수인지 봄
  - 짝수라면 이겼다고 판단하고 이더를 송금해줌
  - block.blockhash(block.number)가 짝/홀수인지 확인하는 새로운 Attacker 컨트랙트를 만들고, 짝수일때 위 컨트랙트의 play()함수를 호출하면 항상 이겼다는 결과가 나와 이더를 송금받을 수 있음
  - block number를 이용해서 난수를 생성할 경우, 한 트랜잭션이 다른 컨트랙트의 함수를 호출했을 때 같은 block number를 사용할 수 있고 이를 악용할 수 있다는 점에서 취약점이 발생할 수 있음을 염두해야 함.
  ![weakness6](./weakness6.png)

<br><br>

#### 7. Front-Running
---
- time-of-check vs time-of-use (TOCTOU), race condition, transaction ordering dependence(TOD)이라고도 부름
- 증권용어로 선형매매라는 말로, **Transaction Ordering 하는 것에 대한 특별한 알고리즘이 없는** 이더리움의 특징을 이용한 취약점을 말함.
(이더리움의 트랜잭션은 **블록에 들어가기 전까지 모든 노드가 그 트랜잭션을 알고** 있어야 하며 트랜잭션이 **처리되는 순서는 miner가 정하기 나름** 이라는 특징을 가짐)

***cf. 선형매매
불법적으로 거래 정보를 미리 알고 거래가 일어나기 전에 매매하는 것을 말함***

- 예시
  - 어떤 퀴즈를 하나 내고 이 문제를 맞춘 사람에게 보상을 주는 함수가 있다고 가정해보자
  - A 라는 사람이 이 문제에 대한 답을 알고 답 제출하는 트랜잭션을 모든 노드에 배포함
  - A가 제출한 답안이 블록에 저장되기 전에, B라는 사람이 A의 답안지를 보고 똑같은 답을 적어서 트랜잭션을 발생시키되 많은 가스를 싣어 보낸다.
  - miner 입장에서는 더 많은 gas가 들어있는 트랜잭션을 처리할테니 B의 답안이 먼저 처리되어 B가 퀴즈를 푼 사람으로서 보상을 받을 수 있다.
  - 이더리움의 Transaction Ordering 알고리즘이 특별히 없음으로 인해 발생할 수 있는 사례이다.

<br><br>

#### 8. Time Manipulation
---
- Miner가 timestamp와 트랜잭션 Ordering를 조작할 수 있다는 점을 이용해 이득을 얻는 취약점
- Code
  - 특정 시간이 지났을 때 play() 함수를 호출자에게 1500이더를 제공하는 함수가 있다고 가정해보자
  - Miner는 Timestamp값과 블록에 담을 Transcation의 순서를 정할 수 있으므로, timestamp를 특정 시간에 가깝게 정해놓고 play()함수를 호출하는 자신의 트랜잭션이 가장 먼저 처리되도록 블록에 담는다.
  - 특정 시간 조금 전에 채굴을 마친 후 모든 노드에 전파하게 되면 네트워크 상의 다른 노드들로부터 accepted 된다.

  ![weakness8](./weakness8.png)

  ***(PoW 합의 알고리즘을 사용한다면 특정 시간 조금 전에 채굴을 마친다는 것이 말처럼 쉬울지 의문이 간다. 이론 상으로는 말이 되지만, 저렇게 하려면 컴퓨팅 파워가 상당해야하고 내가 채굴에 성공한 다음 블록을 배포하기 전에 다른 채굴자가 채굴에 성공해서 배포하는 경우가 나타나선 안된다. 그러면, 또 다시 채굴해야하는 문제가 생기니까...)***

<br><br>

#### 9. Short Address Attack
---
- Off-chain issues라고도 함
- off-chain에서 함수 호출 format이 만들어지는 원리를 교모하게 이용한 취약점

***cf. Off-Chain이란
블록체인이 아닌 SW. 클라이언트나 구현체 어플리케이션을 말함***

- 컨트랙트의 함수를 호출할 때, 보내지는 데이터 Format
  - 함수 해쉬값의 4byte
  - 그 뒤에 함수의 인자가 32byte 단위로 붙음
  (보내는 인자의 크기를 32byte로 맞추기 위해 앞에 남는 비트는 0으로 채움)
  - [이미지 출처][11]

  ![weakness9](./weakness9.png)

  ex) transfer(address \_to, uint256 \_amount) 함수를 호출할 때
  ![weakness9-1](./weakness9-1.png)
  - off-chain client에서 low level으로 컨트랙트 함수를 호출할 수 있다고 가정해보자
  - 실수로 address 정보를 20bytes가 아닌 19bytes만 넣는다면, amount 값은 1bytes가 왼쪽으로 밀려 원래 전달할 값보다 2^8^ 큰 값으로 전달되므로 의도한 것보다 더 많은 이더가 보내지는 문제가 발생할 수 있음
  - 해커가 0으로 끝나는 계정 주소를 만들어서 위와 같은 문제를 악용할 수 있음
  - 이 문제를 해결하기 위해, web3.js의 경우는 check address 기능을 제공하여 실수를 줄이도록 함
  ![weakness9-2](./weakness9-2.png)  




[10]:https://ethereumdev.io/safemath-protect-overflows/
[11]:https://www.youtube.com/watch?v=A5mYsFsypms
