# Go 언어 함수

함수는 특정 작업을 수행하는 코드 블록입니다. Go에서 함수를 정의하고 사용하는 방법을 알아보겠습니다.

## 함수 정의

Go에서 함수는 다음과 같은 구조로 정의합니다.

```go
func Add(a int, b int) int {
    // a와 b를 더한 결과를 반환
    return a + b
}
```

**함수 구성 요소:**
- `func`: 함수 정의 키워드
- `Add`: 함수명 (대문자로 시작하면 외부 패키지에서 접근 가능)
- `(a int, b int)`: 매개변수 (파라미터)
- `int`: 반환 타입
- `{ ... }`: 함수 코드 블록

**매개변수 타입 생략:**
같은 타입의 매개변수는 타입을 한 번만 적을 수 있습니다.
```go
func Add(a, b int) int {  // a int, b int와 같음
    return a + b
}
```

### 기본 함수 실습

```go
package main

import "fmt"

func Add(a int, b int) int {
	return a + b
}

func main() {
	c := Add(3, 6)
	fmt.Println(c)  // 9 출력
}
```

**실행 흐름:**
1. `main()` 함수가 시작됩니다
2. `Add(3, 6)`을 호출합니다
3. `Add()` 함수가 실행되어 9를 반환합니다
4. 반환된 값이 변수 c에 저장됩니다
5. c의 값(9)이 출력됩니다

## 함수를 사용하는 이유

함수를 사용하면 코드의 재사용성이 높아지고 유지보수가 쉬워집니다.

### 함수 호출 스택

함수를 호출할 때는 **스택(Stack)** 자료구조를 사용합니다.
- **LIFO (Last In First Out)**: 후입선출, 나중에 들어온 것이 먼저 나갑니다
- 함수가 호출되면 스택에 쌓이고, 실행이 끝나면 스택에서 제거됩니다

**스택 동작 예시:**
```
main() 호출
  └─> Add(3, 6) 호출
        └─> Add() 실행 후 반환
  └─> main()으로 복귀
```

### 함수 없이 작성한 코드

```go
package main

import "fmt"

func main() {
	math := 80
	eng := 74
	history := 95
	fmt.Println("김일등 님의 평균 점수는", (math+eng+history)/3, "입니다.")

	math = 88
	eng = 92
	history = 53
	fmt.Println("송이등 님의 평균 점수는", (math+eng+history)/3, "입니다.")

	math = 78
	eng = 73
	history = 78
	fmt.Println("박삼등 님의 평균 점수는", (math+eng+history)/3, "입니다.")
}
```

**문제점:**
- 같은 코드가 반복됩니다.
- 평균 계산 방식을 바꾸려면 세 곳을 모두 수정해야 합니다.
- 코드가 길어지고 가독성이 떨어집니다.

### 함수를 사용한 코드

```go
package main

import "fmt"

func PrintAvgScore(name string, math int, eng int, history int) {
	total := math + eng + history
	avg := total / 3
	fmt.Println(name, "님의 평균 점수는", avg, "입니다.")
}

func main() {
	PrintAvgScore("김일등", 80, 74, 95)
	PrintAvgScore("송이등", 88, 92, 53)
	PrintAvgScore("박삼등", 78, 73, 78)
}
```

실행 결과

```bash
김일등 님의 평균 점수는 83 입니다.
송이등 님의 평균 점수는 77 입니다.
박삼등 님의 평균 점수는 76 입니다.
```

**함수 사용의 장점:**
- 코드 재사용성 향상
- 유지보수가 쉬워짐 (한 곳만 수정하면 됨)
- 코드 가독성 향상
- 테스트가 쉬워짐

## 멀티 반환 함수

Go의 강력한 기능 중 하나는 **함수가 여러 값을 반환**할 수 있다는 것입니다.

### 멀티 반환 기본 예제

```go
package main

import "fmt"

func Divide(a, b int) (int, bool) {
	if b == 0 {
		return 0, false
	}
	return a / b, true
}

func main() {
	c, success := Divide(9, 3)
	fmt.Println(c, success)  // 3 true
	
	d, success := Divide(9, 0)
	fmt.Println(d, success)  // 0 false
}
```

**멀티 반환의 활용:**
- 결과값과 함께 성공/실패 여부를 반환
- 결과값과 함께 에러를 반환 (Go의 일반적인 패턴)
- 여러 계산 결과를 동시에 반환

### Named Return (이름이 있는 반환값)

반환 타입에 변수명을 지정하면 명시적으로 반환하지 않아도 됩니다.

```go
package main

import "fmt"

func Divide(a, b int) (result int, success bool) {
	if b == 0 {
		result = 0
		success = false
		return  // result와 success가 자동으로 반환됨
	}
	result = a / b
	success = true
	return
}

func main() {
	c, success := Divide(9, 3)
	fmt.Println(c, success)  // 3 true
	
	d, success := Divide(9, 0)
	fmt.Println(d, success)  // 0 false
}
```

**Named Return의 특징:**
- 함수 선언부에서 반환값의 이름을 지정
- `return`만 쓰면 이름이 지정된 변수들이 자동으로 반환됨
- 코드가 간결해지지만 남용하면 가독성이 떨어질 수 있음

**변수 선언 대입문(:=) 팁:**
```go
d, success := Divide(9, 0)
```
`success`는 이미 선언되어 있지만, `d`가 새로운 변수이므로 `:=`를 사용할 수 있습니다. 여러 변수를 선언할 때 **하나라도 새로운 변수가 있으면** `:=`를 사용할 수 있습니다.

### 반환값 무시하기

반환값 중 일부만 필요한 경우 `_`(언더스코어)로 무시할 수 있습니다.

```go
result, _ := Divide(9, 3)  // success는 무시
fmt.Println(result)  // 3
```

## 재귀 호출 (Recursion)

재귀 호출은 함수 안에서 자기 자신을 다시 호출하는 것을 의미합니다.

### 재귀 호출 기본 예제

```go
package main

import "fmt"

func printNo(n int) {
	if n == 0 {  // 종료 조건 (base case)
		return
	}
	fmt.Println(n)
	printNo(n - 1)  // 재귀 호출
	fmt.Println("After", n)
}

func main() {
	printNo(3)
}
```

실행 결과:

```bash
3
2
1
After 1
After 2
After 3
```

**재귀 호출 동작 과정:**

```
printNo(3) 호출
  ├─ "3" 출력
  └─ printNo(2) 호출
       ├─ "2" 출력
       └─ printNo(1) 호출
            ├─ "1" 출력
            └─ printNo(0) 호출
                 └─ return (종료)
            ├─ "After 1" 출력
            └─ return
       ├─ "After 2" 출력
       └─ return
  ├─ "After 3" 출력
  └─ return
```

**재귀 호출의 핵심:**
1. **종료 조건(Base Case)**: 재귀를 멈추는 조건이 반드시 필요합니다.
2. **재귀 단계(Recursive Case)**: 문제를 작은 단위로 나누어 자기 자신을 호출합니다.

### 팩토리얼 계산 예제

재귀 호출의 대표적인 예시인 팩토리얼을 구현해보겠습니다.

```go
package main

import "fmt"

func factorial(n int) int {
	if n == 1 {  // 종료 조건
		return 1
	}
	return n * factorial(n-1)  // 재귀 호출
}

func main() {
	fmt.Println("5! =", factorial(5))  // 120
}
```

**계산 과정:**
```
factorial(5)
= 5 * factorial(4)
= 5 * 4 * factorial(3)
= 5 * 4 * 3 * factorial(2)
= 5 * 4 * 3 * 2 * factorial(1)
= 5 * 4 * 3 * 2 * 1
= 120
```

### 피보나치 수열 예제

```go
package main

import "fmt"

func fibonacci(n int) int {
	if n <= 1 {  // 종료 조건
		return n
	}
	return fibonacci(n-1) + fibonacci(n-2)  // 재귀 호출
}

func main() {
	for i := 0; i < 10; i++ {
		fmt.Printf("fibonacci(%d) = %d\n", i, fibonacci(i))
	}
}
```

실행 결과:

```bash
fibonacci(0) = 0
fibonacci(1) = 1
fibonacci(2) = 1
fibonacci(3) = 2
fibonacci(4) = 3
fibonacci(5) = 5
fibonacci(6) = 8
fibonacci(7) = 13
fibonacci(8) = 21
fibonacci(9) = 34
```

**재귀 호출 주의사항:**
- 종료 조건이 없으면 무한 루프에 빠집니다.
- 너무 깊은 재귀는 스택 오버플로우를 일으킬 수 있습니다.
- 경우에 따라 반복문보다 성능이 떨어질 수 있습니다.

## Go에서의 Stack관리
Go는 다른 언어에서와는 달리 자동 증가하는 Stack을 사용합니다.
- 전통적인 언어들 : 고정 크기 스택 사용 → 깊은 재귀 시 스택 오버플로우 발생
- Go : 필요에 따라 스택이 자동으로 증가 → 스택 오버플로우가 거의 발생하지 않음