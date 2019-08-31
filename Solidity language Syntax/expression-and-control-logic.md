# Expression and Control Logic

## Expression
- 하나의 값, 객체, 함수를 반환하는 문장(Statement)
- 문장은 여러 개의 피연산자와 0개 이상의 연산자로 이루어짐
- 타 언어처럼 비교 연산자, 논리 연산자, 연산자 우선순위가 존재

## If-Else
- 생략

## Loop
- While
- For
- Do while
- break
- continue

## return
- 두가지 방법이 존재
	1. 반환할 변수의 이름을 정하지 않고 반환하는 방법
	2. 반환할 변수의 이름을 정한 후 반환하는 방법

```solidity
contract ReturnValues {
	uint counter;

	// 반환할 변수의 이름을 지정 X
	function getBlockNumber1() returns (uint) {
		return counter;
	}

	// 반환할 변수의 이름을 지정
	function getBlockNumber2() returns (uint result) {
		result = counter;
	}
}
```
