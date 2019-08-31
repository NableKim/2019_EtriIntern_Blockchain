# Solidity Source File

Solidity Source File은 pragma 지시문, import 지시문과 contract 정의를 포함할 수 있습니다.

## 프라그마(pramga)
- 솔리디티 파일에 사용할 **컴파일러 버전을 지정**하는 지시자
- 특정 컴파일러 기능이나 검사를 활성화하는 데 사용
- 일반적으로 솔리디티 파일의 코드 첫 행에 위치
	ex) pragma solidity ^0.4.19;

- 버전 번호는 메이저 빌드 번호와 마이너 빌드 번호로 구성
- 캐럿(^) 문자는 메이저 버전 내의 최종 버전을 나타냄
	ex)　^0.4.0은 메이저 빌드 번호 4 내의 최종 버전인 0.4.19를 의미

- 소스 파일에 대해 항상 로컬로 적용되므로 모든 프로젝트에서 활성화하려면 모든 파일에 pragma를 추가해야함
- import 지시문을 통해 다른 파일 가져오더라도 pragma는 적용되지 않음
- 기본적으로 활성화되지 않은 컴파일러 또는 언어의 기능을 활성화하는데 사용할 수 있는 experimental pragma가 지원됨

	- ABIEncoderV2
		- 임의로 중첩된 배열과 구조체를 인코딩/디코딩 할 수 있음
		- 이 부분에 대한 최적화 작업이 아직 진행 중
				ex) pragma experimental ABIEncoderV2;
	- SMTChecker
		 - This component has to be enabled when the Solidity compiler is built and therefore it is not available in all Solidity binaries. The build instructions explain how to activate this option. It is activated for the Ubuntu PPA releases in most versions, but not for solc-js, the Docker images, Windows binaries or the statically-built Linux binaries.
		 	ex) pragma experimental SMTChecker;

## 임포트(import)
- import 키워드를 사용
- 현재 솔리디티 파일 및 코드에 다른 솔리디티 파일을 import해서 접근 가능
- 솔리디티 코드를 모듈화하는데 도움이 됨
-  ex) import ‘CommonLibrary.sol’;

## 주석(Comments)
- 단일행, 복수행 주석 모두 가능
- 넷스펙(netspec comment)로 불리는 타입이 존재 >> (///) 와 (/** ... \*/)

```solidity
// 단일 행 주석

/*
복수 행
주석
*/

pragma solidity ^0.4.0;
/** @title 도형 계산기*/
contract shapeCalculator{
    /**
    * @dev 면적과 반둘레를 계산하는 함수.
    * @param w 사격형의 폭.
    * @param h 사각형의 높이.
    * @return s 계산된 사각형의 넓이.
    * @return p 계산된 사각형의 반둘레.
    */
    function rectangle(uint w, uint h) returns (uint s, uint p) {
        s = w * h;
        p =2 * (w + h);
}
```

## Contract, Library, Interface
- 이후 내용에서 설명
