# Go 언어 상수

상수는 프로그램 실행 중에 변경할 수 없는 값을 저장하는 데 사용됩니다.

## 상수의 선언

```go
const ConstValue int = 10
```

**상수 구성 요소:**
- `const`: 상수 선언 키워드
- `ConstValue`: 상수명 (관례적으로 대문자로 시작)
- `int`: 타입 (생략 가능)
- `10`: 값

### 상수의 특징

```go
package main

import "fmt"

func main() {
	const C int = 10

	var b int = C * 20
	fmt.Println(b)  // 200
	
	// C = 20  // 에러! 상수는 값을 변경할 수 없습니다
	// fmt.Println(&C)  // 에러! 상수는 메모리 주소가 없습니다
}
```

**컴파일 에러:**
```bash
./prog.go:11:2: cannot assign to C (declared const)
./prog.go:12:14: cannot take the address of C (declared const)
```

**상수 vs 변수:**
- **상수**: 컴파일 시점에 값이 결정되며, 메모리 주소가 없음
- **변수**: 실행 시점에 값이 할당되며, 메모리 주소가 있음

## 상수를 사용하는 이유

### 1. 변하면 안 되는 값 보호

```go
package main

import "fmt"

func main() {
	const PI1 float64 = 3.141592653589793238  // 상수
	var PI2 float64 = 3.141592653589793238    // 변수

	// PI1 = 4  // 에러! 상수는 변경 불가
	PI2 = 4     // 변수는 변경 가능

	fmt.Printf("원주율(상수): %f\n", PI1)
	fmt.Printf("원주율(변수): %f\n", PI2)
}
```

실행 결과:

```bash
원주율(상수): 3.141593
원주율(변수): 4.000000
```

**상수 사용의 장점:**
- 실수로 값을 변경하는 것을 방지
- 코드의 의도를 명확히 전달
- 컴파일러가 최적화 가능

### 2. 코드값(매직 넘버 제거)

숫자에 의미를 부여하여 코드 가독성을 높입니다.

**나쁜 예 (매직 넘버):**
```go
func PrintAnimal(animal int) {
	if animal == 0 {  // 0이 무엇을 의미하는지 불명확
		fmt.Println("꿀꿀")
	} else if animal == 1 {
		fmt.Println("음메")
	}
}
```

**좋은 예 (상수 사용):**
```go
package main

import "fmt"

const (
	Pig     int = 0
	Cow     int = 1
	Chicken int = 2
)

func PrintAnimal(animal int) {
	if animal == Pig {
		fmt.Println("꿀꿀")
	} else if animal == Cow {
		fmt.Println("음메")
	} else if animal == Chicken {
		fmt.Println("꼬끼오")
	} else {
		fmt.Println("...")
	}
}

func main() {
	PrintAnimal(Pig)
	PrintAnimal(Cow)
	PrintAnimal(Chicken)
}
```

실행 결과:

```bash
꿀꿀
음메
꼬끼오
```

## iota로 열거값 사용하기

값이 순차적으로 증가하는 상수를 정의할 때 `iota` 키워드를 사용하면 편리합니다.

### 기본 iota 사용

```go
const (
	Red   int = iota  // 0
	Blue  int = iota  // 1
	Green int = iota  // 2
)

// 간단하게 표현
const (
	Red   int = iota  // 0
	Blue              // 1 (자동으로 iota 적용)
	Green             // 2 (자동으로 iota 적용)
)
```

**iota 특징:**
- `const` 블록이 시작될 때 0부터 시작
- 각 줄마다 자동으로 1씩 증가
- 같은 줄에서는 같은 값 유지

### iota 응용 - 수식과 함께 사용

```go
const (
	C1 uint = iota + 1  // 1 = 0 + 1
	C2                  // 2 = 1 + 1
	C3                  // 3 = 2 + 1
)

fmt.Println(C1, C2, C3)  // 1 2 3
```

### iota 응용 - 비트 플래그

비트 연산과 함께 사용하여 비트 플래그를 만들 수 있습니다:

```go
const (
	BitFlag1 uint = 1 << iota  // 1 = 1 << 0 (이진수: 0001)
	BitFlag2                   // 2 = 1 << 1 (이진수: 0010)
	BitFlag3                   // 4 = 1 << 2 (이진수: 0100)
	BitFlag4                   // 8 = 1 << 3 (이진수: 1000)
)

fmt.Println(BitFlag1, BitFlag2, BitFlag3, BitFlag4)  // 1 2 4 8
```

**비트 플래그 활용:**
```go
const (
	Read    = 1 << iota  // 1 (0001)
	Write                // 2 (0010)
	Execute              // 4 (0100)
)

// 여러 권한 조합
permission := Read | Write  // 3 (0011)

// 권한 확인
if permission&Read != 0 {
	fmt.Println("읽기 권한 있음")
}
```

### iota 블록 분리

새로운 `const` 블록에서는 iota가 다시 0부터 시작합니다:

```go
const (
	A int = iota  // 0
	B             // 1
)

const (
	C int = iota  // 0 (새로운 블록이므로 다시 0부터)
	D             // 1
)

fmt.Println(A, B, C, D)  // 0 1 0 1
```

### iota 건너뛰기

특정 값을 건너뛰려면 `_`를 사용합니다:

```go
const (
	Apple  = iota  // 0
	_              // 1 건너뜀
	Orange         // 2
	Banana         // 3
)

fmt.Println(Apple, Orange, Banana)  // 0 2 3
```

## 타입 없는 상수 (Untyped Constants)

상수 선언 시 타입을 명시하지 않으면 **타입 없는 상수**가 됩니다.

### 타입 없는 상수의 유연성

```go
package main

import "fmt"

const PI = 3.14              // 타입 없는 상수
const FloatPI float64 = 3.14 // 타입이 있는 상수

func main() {
	var a int = PI * 100       // OK! 타입 없는 상수는 int로 변환 가능
	// var b int = FloatPI * 100  // 에러! float64를 int에 할당 불가

	var c float64 = PI         // OK! 타입 없는 상수는 float64로도 변환 가능
	var d float64 = FloatPI    // OK! 같은 타입

	fmt.Println(a)  // 314
	fmt.Println(c)  // 3.14
	fmt.Println(d)  // 3.14
}
```

**컴파일 에러 (FloatPI 사용 시):**
```bash
./prog.go:8:14: cannot use FloatPI * 100 (constant 314 of type float64) as int value in variable declaration
```

### 타입 없는 상수의 장점

```go
const Size = 10

var a int = Size      // int로 사용
var b int64 = Size    // int64로 사용
var c float64 = Size  // float64로 사용
var d byte = Size     // byte로 사용
```

**타입 없는 상수의 특징:**
- 사용되는 문맥에 따라 자동으로 타입이 결정됩니다
- 더 유연하게 사용할 수 있습니다
- 타입 변환 없이 여러 타입에 사용 가능합니다

### 타입 없는 상수의 정밀도

타입 없는 상수는 컴파일 시점에 매우 높은 정밀도를 가집니다:

```go
const (
	Big   = 1 << 100  // 매우 큰 수 (256비트 정밀도)
	Small = Big >> 99  // 2
)

func main() {
	// var x int = Big  // 에러! int 범위 초과
	var y int = Small   // OK! 사용 시점에 int로 변환
	fmt.Println(y)      // 2
}
```

## 상수 활용 예제

### 시간 관련 상수

```go
const (
	SecondsPerMinute = 60
	SecondsPerHour   = SecondsPerMinute * 60
	SecondsPerDay    = SecondsPerHour * 24
)

func main() {
	fmt.Printf("하루는 %d초입니다\n", SecondsPerDay)  // 86400
}
```

### 파일 권한 상수

```go
const (
	ReadPermission    = 1 << iota  // 1
	WritePermission                // 2
	ExecutePermission              // 4
)

func CheckPermission(userPermission, requiredPermission int) bool {
	return userPermission&requiredPermission != 0
}

func main() {
	myPermission := ReadPermission | WritePermission  // 3
	
	if CheckPermission(myPermission, ReadPermission) {
		fmt.Println("읽기 가능")
	}
	
	if CheckPermission(myPermission, ExecutePermission) {
		fmt.Println("실행 가능")
	} else {
		fmt.Println("실행 불가")
	}
}
```

## 상수 vs 변수 비교

| 특징 | 상수 (const) | 변수 (var) |
|------|-------------|-----------|
| 값 변경 | 불가능 | 가능 |
| 메모리 주소 | 없음 | 있음 |
| 결정 시점 | 컴파일 시점 | 실행 시점 |
| 타입 생략 | 매우 유연 | 타입 추론 |
| 사용 목적 | 고정값, 코드값 | 변경 가능한 값 |