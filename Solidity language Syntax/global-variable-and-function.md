# Global Variables and Function

## 함수
- **상태 변수를 읽거나 상태 변수에 값을 쓰는 메커니즘**
- 컨트랙트 내의 함수를 호출하면 그 결과로 트랜잭션이 생성됨
- 호출을 통해 실행할 수 있는 코드의 단위
- 함수에는 이름이 없어도 됨. 이름 없는 함수가 단 하나 있을 경우를 위해 폴백(fallback) 함수를 제공
- 함수 선언 시 function 키워드, 함수명, 파라미터 나열들은 기존 다른 언어와 형태가 유사하나 그 외에도 Modifier(수정자), 한정자, 여러 개의 파라미터를 반환할 수 있음.
- 함수에 대한 **가시성 한정자**

  |가시성 한정자|설명|
  |------|----------|
  |public|내/외부에서 함수에 직접 접근이 가능|
  |internal|현재 컨트랙트 및 그것을 상속한 컨트랙트 안에서만 사용할 수 있음|
  |private|함수를 선언한 컨트랙트에서만 사용할 수 있음|
  |external|외부에서 직접적으로 접근이 가능하나 내부에서는 접근 불가<br>컨트랙트 인터페이스로 사용<br>함수에 대한 가시성 한정자|


- **상태 변경 가능성(state mutability) 속성**
  - 컨트랙트의 상태 변수를 변경하는 능력과 관련된 동작을 바꾸는 추가적인 한정자

  |속성|설명|
  |---|----|
  |constant|읽기전용. 현재 상태 변수를 읽고 반환할 수 있는 함수<br>블록체인의 상태를 수정할 수 없음|
  |view|constant 함수의 별칭|
  |pure| 상태 변수에 접근(읽고 쓰기)하지 못하며 단순히 연산만 수행하는 함수|
  |payable| 지불. 호출자로부터 이더를 받을 수 있는 함수<br>송금자가 이더를 제공할 때 사용|

- <U>복수의 파라미터를 반환할 수 있음.</U> 이는 다른 프로그래밍 언어에서 찾아보기 힘든 특징
- 튜플을 이용하여 반환값을 할당하여 리턴할 수 있음

```solidity
// 복수개의 파라미터 반환
function multipleOutgoingParameter(int _data) returns (int square, int half) {
	square = _data * _data;
	half = _data /2;
}

// 튜플을 이용하여 반환
function multipleOutgoingParameter(int _data) returns (int square, int half) {
	(square, half) = (_data * _data, _data /2);
}
```

cf. 튜플 : 여러 개의 변수로 구성된 맞춤 자료구조

## var 타입 변수
- 묵시적으로 타입이 정해지는 변수
- 컴파일러는 변수에 할당된 첫 번째 표현식 타입에서 자동으로 타입을 추론
- var는 함수 매개 변수나 반환 매개 변수에선 사용 불가

```solidity
uint24 x =0x123 ;
var y =x ; // y의 타입은 uint24가 됨
```

## 변수 호이스팅(Hoisting)
- 변수를 사용하기 전에 그것을 선언 및 초기화하지 않아도 된다는 개념
- 함수 내 어느 곳에서든지 변수 선언이 일어날 수 있으므로 컴파일러가 컴파일 할 때 함수 내의 모든 변수 선언을 추출해서 처음 또는 함수 시작 부분에 배치하기 때문에 문제없음

## 솔리디티에서의 형 변환
- 묵시적 변환
	- 연산자 사용이나 외부의 도움 없이 변환되는 것을 말함
	- 작은 정수형으로부터 큰 정수형으로 묵시적 변환을 허용
- 명시적 변환
	- 데이터 손실이 우려되거나 데이터가 목표 데이터 타입 범위에 맞아떨어지지 않는 경우 명시적 변환이 요구됨

## 데이터 위치
- 계약 내에서 선언 및 사용되는 **각각의 변수에는 데이터 위치**가 있음
- 모든 복합 타입은 자신이 메모리나 스토리지 중 어디에 저장되었는지를 나타내는 데이터 위치가 추가적으로 존재
- **함수 매개 변수와 리턴 변수의 기본값은 메모리**이고 **상태 변수의 기본값은 스토리지**임

  |데이터 위치|설명|
  |---|-----------------------|
  |Storage|컨트랙트 내의 모든 함수가 사용할 수 있는 **전역 메모리**<br>이더리움 네트워크 내의 모든 노드에 **영구적으로 저장**됨|
  |Memory|컨트랙트 내 모든 함수가 사용할 수 있는 **로컬 메모리**<br>함수가 실행을 마칠 때 사라지는 **단기 휘발성 메모리**|
  |Calldata|모든 입력 **함수의 인자와 실행 데이터를 저장**하는 곳<br>함수 인자가 저장되고 수정 불가능하며 지속성이 없는 calldata라는 데이터 위치가 존재<br>외부 함수의 매개 변수는 calldata에 강제 저장되며 memory처럼 작동|
  |Stack|변수의 적재와 이더리움 인스트럭션 세트를 가지고 작업하는 중간값을 저장하기 위해 스택을 유지<br>EVM의 작업 세트 메모리<br>EVM의 스택 깊이는 1024단계 >> 그 이상을 저장하면 예외 발생|


- 변수 선언 위치와 변수의 자료형에 따라 변수의 데이터 위치가 결정됨. 이 두 요인에 근거하여 변수의 데이터 위치를 정하는 규칙은 다음과 같음

  - 규칙 1
    - **상태 변수**로서 선언되는 변수들은 **항상 Storage**에 저장됨

  - 규칙 2
    - 함수 파라미터로서 선언되는 변수들은 항상 Memory에 저장됨

  - 규칙 3
    - **함수 내에서 선언되는 변수**들은 기본적으로 **Memory**에 저장됨
    - 단, 참조 타입 변수(배열, 구조체, 문자열)의 위치는 스토리지가 기본이며 값 타입 변수의 위치는 함수 내 Memory임.
    - 참조 타입 변수의 위치를 강제로 Memory에 위치시킬 수도 있다.
    - 매핑은 항상 Storage에 선언됨. Memory형으로 선언 불가. 함수 내의 매핑은 상태 변수로 선언된 매핑을 참조할 수 있음.

  - 규칙 4
    - 호출자가 **함수 파라미터에 제공한 인자는 항상 calldata 위치에 저장**됨

  - 규칙 5
    - 다른 상태 변수로부터 상태 변수에 할당할 때, 항상 새로운 사본을 생성


    ```solidity
    pragma solidity ^0.4.19;
    contract DemoStoragetoStorageValueTypeAssignment {
    	uint stateVar1 =20;
    	uint stateVar2 =40;
    	function getUInt() returns (uint) {
    		stateVar1 = stateVar2;
    		stateVar2 =50;
    		return stateVar1;	// 40을 반환
    	}
    }
    ```
    - 배열 타입 상태 변수도 동일함
    ```solidity
    pragma solidity ^0.4.19;
    contract DemoStoragetoStorageReferenceTypeAssignment {
    	uint[2] stateArray1 = [uint(1), 2];
    	uint[2] stateArray2 = [uint(3), 4];
    	function getUInt() returns (uint) {
    		stateArray1 = stateArray2;
    		stateArray2[1] =5;
    		return stateArray1[1];	// 4을 반환
    	}
    }
    ```

  - 규칙 6
    - 다른 Memory 변수로부터 Storage 변수에 할당할 때는 항상 새로운 사본이 생성

    ```solidity  
    pragma solidity ^0.4.19;
    contract DemoMemorytoStorageReferenceTypeAssignment {
    	uint[2] stateArray;
    	function getUInt() returns (uint) {
    		uint[2] memory localArray = [uint(1), 2];
    		stateArray = localArray;
    		localArray[1] =5;
    		return stateArray[1];	// 2을 반환
    	}
    }
    ```

    - 값 타입의 상태 변수에 함수 내 로컬 변수를 할당한 경우도 동일하다.
    ```solidity  
    pragma solidity ^0.4.19;
    contract DemoMemorytoStorageValueTypeAssignment {
    	uint stateVar =20;
    	function getUInt() returns (uint) {
    		uint localVar =40;
    		stateVar = localVar;
    		localVar =50;
    		return stateVar;	// 40을 반환
    	}
    }
    ```



  - 규칙 7
    - 다른 상태 변수로부터 Memory 변수를 할당할 때는 항상 새로운 사본이 만들어진다.
    ```solidity  
    pragma solidity ^0.4.19;
    contract DemoStoragetoMemoryValueTypeAssignment {
    	uint stateVar =20;
    	function getUInt() returns (uint) {
    		uint localVar =40;	// 메모리 변수
    		localVar = stateVar;
    		stateVar =50;
    		return localVar;	// 20을 반환
    	}
    }
    ```


  - 규칙 8
    - 다른 Memory 변수로부터 Memory 변수에 할당할 때는 참조 타입에 사본을 생성하지 않고 값 타입에 대한 새로운 사본을 생성한다.
    ```solidity
    pragma solidity ^0.4.19;
    contract DemoMemorytoMemoryValueTypeAssignment {
    	function getUInt() returns (uint) {
    		uint localVar1 =40;	// 메모리 변수
    		uint localVar2 =80;	// 메모리 변수
    		localVar1 = localVar2;

    		localVar2 =100;
    		return localVar1;	// 80을 반환
    	}
    }
    ```


    - 예외 케이스) Memory 내 참조 타입 변수가 참조에 의해 복사되는 경우
    ```solidity
    pragma solidity ^0.4.19;
    contract DemoMemorytoMemoryReferenceTypeAssignment {
    	function getUInt() returns (uint) {
    		uint[] memory someVar =new uint[](1);
    		someVar[0]=23;
    		uint[] memory otherVar = somVar;	// 참조에 의한 복사
    		someVar[0]=45;
    		return (otherVar[0]); // 45을 반환
    	}
    }
    ```
