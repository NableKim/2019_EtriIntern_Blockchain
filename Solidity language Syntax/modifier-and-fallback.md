# Modifier and Fallback function

## 수정자(Modifier)
- 실행 중인 코드의 행위를 변경하는 구조체
- 언제나 함수와 연관된 것으로 목표 함수보다 먼저 실행되는 함수로 생각하면 됨
- 유효성 검증이나 진위 확인을 위한 규칙으로 깔끔하게 함수를 작성하는 데 도움이 됨

```solidity
modifier onlyBy() {
	if(msg.sender == personIdentifier) {
		_;	// 목표 함수 내용 실행
	}
}
```

## 폴백(Fallback) 함수
- 이더리움에만 있는 특수 함수
- 인자, 반환값을 가질 수 없으며 external 가시성을 가져야 하고 명시적으로 실행될 수없음
- 폴백 함수가 호출 되는 경우
	- 계약 내에 존재하지 않는 함수명을 사용하는 경우
	- 컨트랙트가 data없이 순수 이더를 송금받는 경우. 이더를 받는 함수에는 payable을 붙여야 함
- 따로 식별자나 함수명이 없이 정의됨

```solidity
contract FallbackFunction {
 function() {
  var a = 0;
 }
}
```
