># 블록체인 관련 주요 용어

## 화폐의 구분
### 암호화폐(Crytocurrency)
- 가상화폐에 해당
- 디지털 서명, 해쉬 체인 등의 암호학 기반 기술을 활용한 블록체인 기반 전자화폐를 말함

### 피아트 화폐(Fiat Money)
- 실질적 가치와는 관계 없이 표시된 가격으로 통용되는 일반적인 화폐
  ex) 원, 달러
- 암호화폐와 반대 의미



## 튜링 완전(Turing Completeness)
- 어떤 프로그래밍 언어나 추상 기계가 튜링 기계와 동일한 계산 능력을 가진다는 의미
- 튜링 기계로 풀 수 있는 문제(계산적인 문제)를 그 프로그래밍 언어나 추상 기계로 풀 수 있다는 의미
- ex) 파스칼, C++, XSLT
- 출처 : [위키백과][1]



## 지갑(Wallet)
- 암호화폐 거래에 필요한 개인 키를 저장한 공간



## 거래(Transaction)
- 일반적으로 시스템 안에서 더 나눌 수 없는 처리 단위를 의미
- 블록체인에서는 코인과 토큰 소유권을 포함하는 데이터를 주고받는 것을 말함



## 블록(Block)
- 여러 거래를 모아 만든 데이터 단위
- 거래를 블록에 저장하면 올바른 거래인지 검증하며,  합의 알고리즘 등을 이용해 이중 지급을 막음
- 블록 안 포함할 트랜잭션과 타임스탬프는 블록을 만든 사람이 자유롭게 설정 가능 >> 타임스탬프 기록으로 블록 생성 순서를 판단하는 건 비신뢰적임. 따라서, **직전 블록의 해쉬값**을 새로 생성할 블록에 저장해 블록 생성 순서의 신뢰도를 보장



## 타임스탬프(Timestamp)
- 날짜와 시간을 나타내는 문자열
- 실제 거래를 생성한 시각과 블록에 정식으로 저장한 시각을 타임스탬프에 저장하는데 이 두 기록이 일치하지않아 문제가 발생함. 주의해야함



## 채굴(Mining)
- 새로운 블록 생성에 참여해 보상으로 암호화폐를 얻는 행동



## 메인넷(Main Net)과 테스트넷(Test Net)
- 메인넷
  - 독립적인 암호화폐로 인정하는 프로그램을 출시/운용하는 네트워크
- 테스트넷
  - 블록체인 어플리케이션을 개발할 때 사용하는 메인넷과 같은 구조의 네트워크




## 코인과 토큰
- 코인 : 메인넷이 있는 시스템에서 발행한 암호화폐
- 토큰 : 메인넷의 블록체인 시스템을 빌려 발행한 독자적인 암호화폐

### 토큰(Token)

1. Fungible Token Standard (ERC-20)
    - uniformity와 divisibility 특성을 갖는 Token
    - 각 Token이 동일한 가치를 지니므로 상호 교환이 가능
    - 블록체인 Tokens의 상당 부분이 Fungible Token임
    - ERC-20-compatible tokens들은 open-zepplin에서 제공하는 erc-20 토큰 표준 인터페이스를 구현해야 함
    - https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9b3710465583284b8c4c5d2245749246bb2e0094/contracts/token/ERC20/ERC20.sol

2. Non-fungible Token Standard (ERC-721)
    - 고유 자산을 나타내는 특수한 유형의 Token
    - 모든 단일 Token은 고유하고 나눌 수 없음
    - 디지털 아트, 게임 아이템 등 모든 종류의 자산을 나타내고 사람들이 거래할 수 있음
    - ERC-721-compatible tokens들은 open-zepplin에서 제공하는 erc-721 토큰 표준 인터페이스를 구현해야 함
    - https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol



## 확정(Confirmation)
- 거래를 블록에 저장시켜 기존의 거래 내역과 새로운 거래 내역을 검증하는 것
- 어떤 거래 하나가 저장된 블록과 연결된 다른 블록의 개수만큼 반복하여 거래 내역 검증 작업이 이뤄짐. <U>***잘 이해안됨***</U>
- 연결된 블록의 개수 : 확정 횟수
- 확정 횟수가 많을수록 확정한 거래 내역의 신뢰도를 보장할 수 있어서 신뢰도를 나타내는 지표로 이용



## 가스(Gas)
- 네트워크 수수료
- 악성 프로그램의 실행을 막는 역할을 수행하며 이더리움을 유지하는 비용으로도 이용됨



## EIPs(Ethereum Improvemnet Proposals)
- 이더리움 네트워크의 개선안을 제안하는 곳



## ERC20
- ERC는 EIPs에서 관리하는 공식 프로토콜로 Ethereum Request for Comments를 줄임말
- ERC20은 ERC에서 정한 표준 토큰 스펙을 의미
- 토큰 이름, 통화 코드, 화폐 총공급량, 토큰 전달 방법 등이 정의되어 있음



## 블록체인 2.0
- 암호화폐 이외의 분야에서 블록체인을 사용하는 것



## 블록체인 컨소시엄
- 블록체인 기반 P2P 네트워크로 같은 분야의 기업이나 단체가 필요한 정보를 공유하는 연구
ex) 여러 병원 나뉘어 있는 환자의 의료 기록을 안전하게 공유, 취업 사이트에 등록한 지원자의 이력서에 거짓이 없음을 보증하는 서비스



## 해쉬 트리(Hash Tree)
- 키 하나를 사용해 여러 데이터에 디지털 서명을 부여



## 해쉬 체인(Hash Chain)
- 키 하나로 여러 개의 일회용 비밀번호를 만듦



## R3 컨소시엄
- 금융 업계 주도로 블록체인 기반 기술인 분산 원장 플랫폼을 제공하기 위해 만들어진 조직(2015년 9월)



## 하이퍼레저 프로젝트(Hyperledger Project)
- 리눅스 재단에서 블록체인 기반 오픈 소스 소프트웨어를 개발하는 프로젝트(2015년 12월)



## 원자 교환(Atomic Swap)
- 다른 블록체인 플랫폼 사이에 암호화폐를 교환하는 기술(2017년 후반)



## 라이트닝 네트워크(Lightning Network)
- 빠른 속도로 암호화폐를 거래하면서도 거래 수수료는 상대적으로 낮은 기술(2017년 후반)



## 알트코인
- 비트코인 이후에 등장한 암호화폐를 지칭하는 용어



## UTXO
- 비트코인 블록체인에서 구현한 송금 시스템의 자료구조



## ICO(Initial Coin Offering)
- Dapp이 토큰을 만들어서 이를 공개적으로 판매하는 것
- 새로운 기업이 상장되어 주식이 공개적으로 유통되는 IPO(Initial Public Offering)에 빗대어 만들어진 말
- 실체가 없는 상태에서 계획만 가지고 토큰을 판매하기 때문에 크라우드 펀딩에 더 가까움



[1]:https://ko.wikipedia.org/wiki/%ED%8A%9C%EB%A7%81_%EC%99%84%EC%A0%84
