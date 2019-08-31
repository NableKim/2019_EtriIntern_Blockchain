# Type

 Solidity는 컴파일 시점에 각 변수(상태 변수와 지역 변수)의 타입이 명시되어야 하는 **정적 타입 언어**이다. 기본 타입을 조합해서 복합 타입을 만들 수 있다.

 [Solidity 공식 홈페이지에서 제공하는 타입에 대한 설명 자료][1]


## 값 타입
 - 자체적인 메모리에 **데이터(값)을 직접적으로 갖는 경우** 값 타입이라 함
 - 32바이트 이상의 메모리를 점유하지 않음

### Booleans
- 참(true)와 거짓(false)
- 타 프로그래밍 언어에서와 달리 <U>solidity의 Boolean은 정수로 변환되지 않음</U>
- 연산자 (||)와 (&&)에는 일반적인 short-circuiting rules이 적용됨
	cf. short-circuiting rules
	식 f(x) || g(y)에서 만약 f(x)가 true로 확인되면, g(y)는 확인하지 않음

### Integers
- 다양한 크기의 부호있는 정수 타입, 부호없는 정수 타입이 존재
- int8~int256, uint8~uint256까지 8비트 단위로 키워드가 존재
- uint == uint256, int == int256
- 비교 연산자, 비트 연산자, 산술 연산자 지원
- 나눗셈 결과는 항상 정수. 소수 부분은 절사됨
- (단, 두 연산자가 리터럴인 경우 소수분은 절사되지 않음)
- 0으로 나누거나 모듈로 연산을 하면 런타임 예외 발생
- 시프트 연산(x<<y, x>>y)에서 y가 음수면 런타임 예외 발생

### Fixed Point Numbers
- <U>고정 소수점 수는 아직 Solidity에서 완벽하게 지원되지 않음</U>
- 고정 소수점 수는 선언될 순 있으나 할당될 순 없음
- 다양한 크기의 부호있는 고정 소수점(fixed)와 부호없는 고정 소수점(ufixed)가 존재
- ufixedMxN, fixedMxN로 표현
  (여기서 M은 타입에 의해 취해진 비트 수로 8~256비트 사이의 값,  N은 소수점이하 자리수를 의미하며 0~80 사이값을 가져야 함)

### Address
- 이더리움 계정 주소를 담기 위해 설계됨
- 20바이트 크기를 가짐
- address형과 address payable 두 가지 형태로 구분됨. 둘이 거의 비슷하나 address payable은 이더를 받을 수 있지만 address 타입은 이더를 받을 수 없음
- address payable에서 address형으로 묵시적 형변환이 가능. 반대는 X
- address 타입에는 멤버가 있으며 모든 컨트랙트의 기반이 됨
- 해당 계정에서 사용 가능한 이더의 총량을 반환하는 balance 속성이 있음
- 이더를 송금하는 transfer, send 함수가 있음. (주소 쪽으로 돈 송금)
  - send는 이더 송금의 성공 여부만 불린 형식으로 반환
  - transfer는 예외를 일으키고 호출자에게 이더를 반환 >> transfer를 사용하자

```solidity
function sendEther(address _recipent) public payable returns (bool) {
  require(whitelist[_recipent], "WhiteList에 존재하지 않는 수신자입니다.");
  require(msg.value >0, "Ether 송금액이 0 이상이여야 합니다.");

  address(_recipent).transfer(msg.value);

  return true;
}
```


- 계약 함수를 호출하는 Call, DelegateCall, Callcode 함수가 있음
- [address 멤버에 대한 설명][2]

### Address 리터럴, 유리수 리터럴, 정수 리터럴, 문자열 리터럴 등
- 공식 문서 참조

### Enum
- 사용자 정의 타입을 만드는 한 가지 방법
- 모든 정수타입으로 또는 정수타입에서 명시적 변환이 가능
- but, 암시적 변환 불가
- 미리 정의된 상수값들의 목록
- 타 언어처럼 enum 키워드를 사용하여 쓸 수 있음
- 함수 내에 선언 X, 컨트랙트의 전역 이름공간 내에서 선언
- <U>web3와 DApp은 컨트랙트 내 선언된 enum을 이해하지 못하므로 리턴형으로 열거형을 줄 경우 문제가 생길 수 있음</U>
```solidity
contract Enums {
	// 열거형 선언
	enum status {created, approved, provisioned, rejected, deleted}
	// 초기값이 2인 열거형 인스턴스
	status myStatus = status.provisioned;
	// 리턴형이 status
	fuction returnEnum() returns (status) {
		status stat = status.created;
		return stat;
	}
	// 리턴형이 uint
	fuction returnEnumInt() returns (uint) {
		status stat = status.created;
		return uint(stat);
	}
}
```


### 함수 타입
- 함수 타입의 변수
- 함수 타입에는 두 종류 - 내부 함수와 외부 함수
  - 내부 함수는 현재 컨트랙트의 내부에서만 호출될 수 있음
  - 외부 함수는 address와 함수 signature로 구성되며 외부 함수 호출을 통해 전달되고 반환될 수 있음
- Default 함수 타입은 internal. 생략가능
- 컨트랙트에서 함수에 접근하는 방법은 [함수 이름]을 사용하거나 [this.함수 이름]을 사용하여 접근할 수 있음
- 함수 타입 표기 방법

```solidity
function (<parameter types >) {internal |external} [pure |constant |view |payable] [returns (<return types >)]
```

cf. <U>람다 함수나 인라인 함수 역시 계획되어 있지만 아직 지원되지 않음</U>

## 참조 타입
 - 값을 변수에 직접 저장하지 않고 값이 저장된 위치의 주소를 저장함
 - 실제 데이터가 저장된 메모리 위치에 대한 포인터
 - 32바이트 이상인 메모리를 가질 수 있음
 - 고정 배열과 동적 배열 사용 가능
 - 동적 배열의 경우, 인라인으로 초기화하거나 new 연산자를 사용해서 초기화 가능
 - Storage에 할당된 동적 배열은 length 속성을 이용해 size 변경이 가능하나 memory에 할당된 동적 배열은 size 변경이 불가능
 cf. 동적 배열 : 배열의 크기가 실행 시간에 결정되는 배열

### Fixed-size byte array 고정 크기 바이트 배열
- bytes1~bytes32
- byte == bytes1
- 비교 연산자, 비트연산자 지원
- 인덱스 접근 가능

### Dynamically-sized byte array 동적 크기 바이트 배열
- bytes 배열
  - 여러개의 bytes를 담을 수 있는 동적 배열
  - 모든 바이트를 한데 묶어서 사용함
  - length 속성을 제공
  - 임의 길이의 원시 바이트 데이터의 경우에는 bytes 사용을 권장

```solidity
bytes loacalBytes;
localBytes =new bytes (10);	// 길이 10인 bytes 배열을 생성
localBytes ="KSS";
```

- String 배열
  - bytes 배열과 거의 유사하나 추가적인 제약이 있음
  - index, push, length를 사용X
  - bytes로 변환 가능
  - 임의 길이의 문자열(UTF-8) 데이터의 경우에는 string 사용을 권장

### Struct 구조체
- 구조체 인스턴스를 생성하는 구문은 아래와 같음
- 구조체가 동일한 구조체 타입의 멤버를 포함할 수 없음
- 구조체 인스턴스 생성시 new 키워드 사용하지 않음

```solidity
// myStruct 구조체 사용
human = muStruct(“Ritesh”, 10, true, new uint[](3));
new 키워드는 컨트랙트 인스턴스 또는 배열을 생성할 때만 사용
```

### 매핑 mapping
- key-value 쌍을 저장
- 타 언어의 해시 테이블 또는 딕셔너리와 유사
- 매핑은 상태변수 또는 내부 함수에서의 Storage 참조 타입에만 사용 가능
- <U>But, 솔리디티에서는 매핑에 대해 직접적으로 이터레이션이 불가</U>
	\>\> 이더리움에서 이터레이팅과 루핑은 가스 비용이 많이 드는 연산이기 때문

[1]: https://solidity.readthedocs.io/en/develop/types.html
[2]: https://solidity-kr.readthedocs.io/ko/latest/units-and-global-variables.html#address-related
