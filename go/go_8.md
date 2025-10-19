# Go 언어 switch 문

switch 문은 여러 조건을 비교할 때 if-else 문보다 가독성 좋게 작성할 수 있는 제어문입니다.

## 기본 사용법

```go
switch 비교값 {  // 검사하는 값
    case 값1:    // 비교값과 값1이 같을 때 수행
        문장
    case 값2:    // 비교값과 값2가 같을 때 수행
        문장
    default:     // 만족하는 case가 없을 때 수행
        문장
}
```

**Go의 switch 문 특징:**
- 비교값을 괄호 `()`로 감싸지 않습니다
- case 블록이 **자동으로 break** 됩니다 (다른 언어와 다름!)
- 여러 case를 쉼표로 구분하여 동시에 비교 가능

### 기본 switch 실습

```go
package main

import "fmt"

func main() {
	a := 3

	switch a {
	case 1:
		fmt.Println("a == 1")
	case 2:
		fmt.Println("a == 2")
	case 3:
		fmt.Println("a == 3")
	case 4:
		fmt.Println("a == 4")
	default:
		fmt.Println("a > 4")
	}
}
```

실행 결과:

```bash
a == 3
```

**동작 방식:**
- a의 값(3)과 각 case를 순서대로 비교
- case 3이 일치하므로 해당 블록 실행
- **자동으로 switch 문 종료** (break 불필요)

## switch 문을 사용하는 이유

복잡한 if-else 문을 switch 문으로 바꾸면 가독성이 크게 향상됩니다.

### if-else 문 사용 (가독성 낮음)

```go
package main

import "fmt"

func main() {
	day := 3

	if day == 1 {
		fmt.Println("첫째 날입니다.")
	} else if day == 2 {
		fmt.Println("둘째 날입니다.")
	} else if day == 3 {
		fmt.Println("셋째 날입니다.")
	} else if day == 4 {
		fmt.Println("넷째 날입니다.")
	} else if day == 5 {
		fmt.Println("다섯째 날입니다.")
	} else {
		fmt.Println("최종일입니다.")
	}
}
```

**문제점:**
- else if가 반복되어 코드가 장황함
- 동작을 한눈에 파악하기 어려움
- 들여쓰기가 계속 증가

### switch 문 사용 (가독성 높음)

```go
package main

import "fmt"

func main() {
	day := 3

	switch day {
	case 1:
		fmt.Println("첫째 날입니다.")
	case 2:
		fmt.Println("둘째 날입니다.")
	case 3:
		fmt.Println("셋째 날입니다.")
	case 4:
		fmt.Println("넷째 날입니다.")
	case 5:
		fmt.Println("다섯째 날입니다.")
	default:
		fmt.Println("최종일입니다.")
	}
}
```

실행 결과:

```bash
셋째 날입니다.
```

**장점:**
- 구조가 명확하고 읽기 쉬움
- 비교 대상(day)이 명확히 보임
- 일관된 들여쓰기로 깔끔함

## 다양한 switch 문의 형태

### 1. 한 번에 여러 값 비교

쉼표로 구분하여 여러 값을 한 case에 넣을 수 있습니다.

```go
package main

import "fmt"

func main() {
	day := "thursday"

	switch day {
	case "monday", "tuesday":
		fmt.Println("월, 화요일은 수업이 있습니다.")
	case "wednesday", "thursday", "friday":
		fmt.Println("수, 목, 금요일은 실습이 있습니다.")
	default:
		fmt.Println("주말입니다.")
	}
}
```

실행 결과:

```bash
수, 목, 금요일은 실습이 있습니다.
```

**동작 방식:**
- day가 "wednesday", "thursday", "friday" 중 하나라도 일치하면 실행
- OR 조건과 같은 효과

### 2. 조건문 비교 (switch true)

비교값 없이 `switch true`를 사용하면 각 case에 조건문을 넣을 수 있습니다.

```go
package main

import "fmt"

func main() {
	temp := 18

	switch true {
	case temp < 10, temp > 30:
		fmt.Println("좋은 날씨가 아닙니다.")
	case temp >= 10 && temp < 20:
		fmt.Println("약간 추울 수 있습니다.")
	case temp >= 15 && temp < 25:
		fmt.Println("좋은 날씨입니다.")
	default:
		fmt.Println("따뜻합니다.")
	}
}
```

실행 결과:

```bash
약간 추울 수 있습니다.
```

**주의:** 조건이 겹치면 **먼저 나오는 case**가 실행됩니다.

### 3. 비교값 생략 (switch true와 동일)

`switch true`는 비교값을 생략할 수 있습니다:

```go
switch {  // switch true와 동일
case temp < 10:
    fmt.Println("추움")
case temp < 20:
    fmt.Println("서늘함")
default:
    fmt.Println("따뜻함")
}
```

이 형태는 if-else if 체인을 대체하는 깔끔한 방법입니다!

## switch 초기문

if 문과 마찬가지로 switch 문도 초기문을 사용할 수 있습니다.

### 기본 문법

```go
switch 초기문; 비교값 {
case 값1:
    ...
case 값2:
    ...
default:
    ...
}
```

### switch 초기문 실습 1

```go
package main

import "fmt"

func getMyAge() int {
	return 22
}

func main() {
	switch age := getMyAge(); age {
	case 10:
		fmt.Println("Teenage")
	case 33:
		fmt.Println("Pair 3")
	default:
		fmt.Println("My age is", age)
	}

	// fmt.Println("age is", age)  // 에러! age는 switch 블록 밖에서 사용 불가
}
```

실행 결과:

```bash
My age is 22
```

### switch 초기문 실습 2 (조건문과 함께)

```go
package main

import "fmt"

func getMyAge() int {
	return 22
}

func main() {
	switch age := getMyAge(); true {
	case age < 10:
		fmt.Println("Child")
	case age < 20:
		fmt.Println("Teenage")
	case age < 30:
		fmt.Println("20s")
	default:
		fmt.Println("My age is", age)
	}
}
```

실행 결과:

```bash
20s
```

**switch 초기문의 특징:**
- 초기문에서 선언된 변수는 **switch 블록 안에서만** 사용 가능
- 변수 스코프를 제한하여 코드를 깔끔하게 유지
- 모든 case 블록에서 사용 가능

## const 열거값과 switch

타입과 상수를 활용하여 안전한 switch 문을 작성할 수 있습니다.

```go
package main

import "fmt"

type ColorType int

const (
	Red ColorType = iota
	Blue
	Green
	Yellow
)

func colorToString(color ColorType) string {
	switch color {
	case Red:
		return "Red"
	case Blue:
		return "Blue"
	case Green:
		return "Green"
	case Yellow:
		return "Yellow"
	default:
		return "Undefined"
	}
}

func getMyFavoriteColor() ColorType {
	return Blue
}

func main() {
	fmt.Println("My favorite color is", colorToString(getMyFavoriteColor()))
}
```

실행 결과:

```bash
My favorite color is Blue
```

**장점:**
- 타입 안정성: ColorType만 받을 수 있음
- 명확한 의미 전달
- 오타 방지
- IDE의 자동완성 지원

## break와 fallthrough 키워드

### Go의 독특한 switch 동작

**다른 언어 (C, Java 등):**
- case가 끝나면 다음 case로 자동 진행 (fall-through)
- 명시적으로 `break`를 써야 멈춤

**Go:**
- case가 끝나면 **자동으로 switch 종료** (auto-break)
- 다음 case로 진행하려면 명시적으로 `fallthrough` 사용

### break 키워드 (선택적)

Go에서 break는 기본 동작이므로 명시하지 않아도 됩니다:

```go
package main

import "fmt"

func main() {
	a := 3

	switch a {
	case 1:
		fmt.Println("a == 1")
		break  // 생략 가능 (기본 동작)
	case 2:
		fmt.Println("a == 2")
		break  // 생략 가능
	case 3:
		fmt.Println("a == 3")  // break 없어도 자동 종료
	case 4:
		fmt.Println("a == 4")
	default:
		fmt.Println("a > 4")
	}
}
```

실행 결과:

```bash
a == 3
```

**참고:** break는 중첩된 반복문에서 switch를 빠져나갈 때 유용합니다.

### fallthrough 키워드

다음 case를 **조건 없이** 실행하고 싶을 때 사용합니다:

```go
package main

import "fmt"

func main() {
	a := 3

	switch a {
	case 1:
		fmt.Println("a == 1")
	case 2:
		fmt.Println("a == 2")
	case 3:
		fmt.Println("a == 3")
		fallthrough  // 다음 case를 무조건 실행
	case 4:
		fmt.Println("a == 4")
	case 5:
		fmt.Println("a == 5")
	default:
		fmt.Println("a > 5")
	}
}
```

실행 결과:

```bash
a == 3
a == 4
```

**fallthrough 주의사항:**
- **다음 case의 조건을 확인하지 않고** 무조건 실행
- 가장 마지막 case나 default에서는 사용 불가
- 남용하면 코드 가독성이 떨어짐

### fallthrough 활용 예시

```go
package main

import "fmt"

func main() {
	grade := "B"

	switch grade {
	case "A":
		fmt.Println("훌륭합니다!")
		fallthrough
	case "B":
		fmt.Println("잘했습니다!")
		fallthrough
	case "C":
		fmt.Println("합격입니다!")
	case "D":
		fmt.Println("재시험이 필요합니다.")
	default:
		fmt.Println("불합격입니다.")
	}
}
```

실행 결과:

```bash
잘했습니다!
합격입니다!
```

## switch 문 활용 팁

### 1. 타입 switch (Type Assertion)

인터페이스의 실제 타입을 확인할 때 사용합니다:

```go
func checkType(value interface{}) {
	switch v := value.(type) {
	case int:
		fmt.Printf("정수: %d\n", v)
	case string:
		fmt.Printf("문자열: %s\n", v)
	case bool:
		fmt.Printf("불리언: %t\n", v)
	default:
		fmt.Printf("알 수 없는 타입: %T\n", v)
	}
}

func main() {
	checkType(42)
	checkType("hello")
	checkType(true)
}
```

실행 결과:

```bash
정수: 42
문자열: hello
불리언: true
```

### 2. switch vs if-else 선택 기준

**switch를 사용하기 좋은 경우:**
- 하나의 변수를 여러 값과 비교할 때
- 명확한 케이스들이 나열되는 경우
- 상수나 열거값과 비교할 때

**if-else를 사용하기 좋은 경우:**
- 복잡한 논리 조건이 필요할 때
- 서로 다른 변수들을 비교할 때
- 조건이 2~3개 정도로 적을 때

### 3. 빈 case 사용

특정 값에 대해 아무 동작도 하지 않으려면 빈 case를 사용합니다:

```go
switch day {
case "saturday", "sunday":
    // 주말은 아무것도 안 함
case "monday":
    fmt.Println("월요일입니다")
}
```

## 정리
Go의 switch 문은 다른 언어보다 더 강력하고 유연합니다. 특히 자동 break와 조건문 형태는 코드를 훨씬 깔끔하게 만들어줍니다!