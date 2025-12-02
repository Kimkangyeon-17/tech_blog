# Go 언어 if 문

조건문은 프로그램의 흐름을 제어하는 가장 기본적인 구조입니다. Go의 if 문 사용법을 알아보겠습니다.

## 기본 사용법

```go
if 조건문 {
    문장
} else if 조건문 {
    문장
} else {
    문장
}
```

**Go의 if 문 특징:**
- 조건문을 감싸는 괄호 `()`가 **필요 없습니다**
- 중괄호 `{}`는 **반드시 필요합니다**
- 여는 중괄호 `{`는 **같은 줄**에 있어야 합니다

### 기본 if 문 실습

```go
package main

import "fmt"

func main() {
	temp := 33

	if temp > 28 {
		fmt.Println("에어컨을 켠다")
	} else if temp <= 3 {
		fmt.Println("히터를 켠다")
	} else if temp <= 18 {
		fmt.Println("나가자")
	} else {
		fmt.Println("덥다")
	}
}
```

실행 결과:

```bash
에어컨을 켠다
```

**조건 평가 순서:**
1. `temp > 28` → true이므로 "에어컨을 켠다" 출력 후 종료
2. 나머지 조건들은 평가되지 않습니다

### Go만의 스타일

```go
// Go 스타일 (올바름)
if x > 0 {
    fmt.Println("positive")
}

// C/Java 스타일 (Go에서는 에러)
if (x > 0) {
    fmt.Println("positive")
}

// 중괄호 위치 잘못 (Go에서는 에러)
if x > 0 
{
    fmt.Println("positive")
}
```

## 논리 연산자: AND(&&), OR(||)

여러 조건을 결합하여 복잡한 조건문을 만들 수 있습니다.

### 논리 연산자 실습

```go
package main

import "fmt"

func main() {
	var age = 22

	if age >= 10 && age <= 15 {
		fmt.Println("You are young")
		// age가 10 이상 15 이하인 경우
	} else if age > 30 || age < 20 {
		fmt.Println("You are not 20s")
		// age가 30보다 크거나 20보다 작은 경우, 즉 20대가 아닌 경우
	} else {
		fmt.Println("Best age of your life")
	}
}
```

실행 결과:

```bash
Best age of your life
```

**조건 평가:**
- `age >= 10 && age <= 15` → `22 >= 10 && 22 <= 15` → false
- `age > 30 || age < 20` → `22 > 30 || 22 < 20` → false
- 모든 조건이 false이므로 else 블록 실행

## 단락 평가 (Short-circuit Evaluation)

Go는 논리 연산에서 **단락 평가**를 사용합니다.

**단락 평가란?**
- `&&`: 왼쪽이 false면 오른쪽을 평가하지 않음
- `||`: 왼쪽이 true면 오른쪽을 평가하지 않음

### 단락 평가 실습

```go
package main

import "fmt"

var cnt int = 0

func IncreaseAndReturn() int {
	fmt.Println("IncreaseAndReturn() 호출됨, cnt =", cnt)
	cnt++
	return cnt
}

func main() {
	// false && ... : && 왼쪽이 false이므로 오른쪽 평가 안 함
	if false && IncreaseAndReturn() < 5 {
		fmt.Println("1 increase")
	}

	// true || ... : || 왼쪽이 true이므로 오른쪽 평가 안 함
	if true || IncreaseAndReturn() < 5 {
		fmt.Println("2 increase")
	}

	fmt.Println("최종 cnt:", cnt)
}
```

실행 결과:

```bash
2 increase
최종 cnt: 0
```

**결과 분석:**
- `IncreaseAndReturn()` 함수가 **한 번도 호출되지 않았습니다**
- 첫 번째: `false &&` 이므로 오른쪽 평가 안 함
- 두 번째: `true ||` 이므로 오른쪽 평가 안 함
- 따라서 cnt는 0으로 유지됩니다

### 단락 평가 활용 예시

```go
// 0으로 나누기 방지
if divisor != 0 && number/divisor > 10 {
    fmt.Println("큰 값")
}

// nil 체크 후 접근
if ptr != nil && ptr.Value > 0 {
    fmt.Println(ptr.Value)
}
```

## 중첩 if

if 문 안에 다른 if 문을 포함할 수 있습니다.

### 중첩 if 실습

```go
package main

import "fmt"

func HasRichFriend() bool {
	return true
}

func GetFriendCount() int {
	return 3
}

func main() {
	price := 35000

	if price > 50000 {
		if HasRichFriend() {
			fmt.Println("앗 신발끈이 풀렸네")
		} else {
			fmt.Println("나눠내자")
		}
	} else if price >= 30000 && price <= 50000 {
		if GetFriendCount() > 3 {
			fmt.Println("어이쿠")
		} else {
			fmt.Println("사람도 얼마 없는데 나눠 내자")
		}
	} else {
		fmt.Println("오늘은 내가 쏜다")
	}
}
```

실행 결과:

```bash
사람도 얼마 없는데 나눠 내자
```

**실행 흐름:**
1. `price > 50000` → false
2. `price >= 30000 && price <= 50000` → true
3. `GetFriendCount() > 3` → `3 > 3` → false
4. else 블록 실행

**중첩 if 주의사항:**
- 너무 깊은 중첩은 코드 가독성을 떨어뜨립니다
- 3단계 이상의 중첩은 피하는 것이 좋습니다
- 필요하다면 함수로 분리하거나 early return을 고려하세요

## if 초기문 (Go만의 특별한 기능)

Go의 if 문은 조건문 앞에 **초기화 구문**을 넣을 수 있습니다.

### 기본 문법

```go
if 초기문; 조건문 {
    문장
}
```

### if 초기문 실습

```go
package main

import "fmt"

func getMyAge() (int, bool) {
	return 33, true
}

func main() {
	if age, ok := getMyAge(); ok && age < 20 {
		fmt.Println("You are young", age)
	} else if ok && age < 30 {
		fmt.Println("Nice age", age)
	} else if ok {
		fmt.Println("You are beautiful", age)
	} else {
		fmt.Println("Error")
	}

	// fmt.Println("Your age is", age)  // 에러! age는 if 블록 밖에서 사용 불가
}
```

실행 결과:

```bash
You are beautiful 33
```

**if 초기문의 스코프:**
```bash
# age 변수를 if 블록 밖에서 사용하려고 하면 에러 발생
./prog.go:18:29: undefined: age
```

**중요한 특징:**
- 초기문에서 선언된 변수는 **if 블록 안에서만** 사용 가능
- if-else if-else 체인 전체에서는 사용 가능
- 블록이 끝나면 변수는 사라집니다

### if 초기문의 장점

```go
// 초기문 없이 (변수가 외부에 노출됨)
age, ok := getMyAge()
if ok && age < 20 {
    fmt.Println("Young")
}
// age와 ok가 여기서도 접근 가능 (불필요한 노출)

// 초기문 사용 (변수 스코프를 제한)
if age, ok := getMyAge(); ok && age < 20 {
    fmt.Println("Young")
}
// age와 ok는 여기서 접근 불가 (스코프 제한)
```

**if 초기문 활용 예시:**

```go
// 파일 읽기
if data, err := readFile("test.txt"); err == nil {
    fmt.Println("파일 내용:", string(data))
} else {
    fmt.Println("파일 읽기 실패:", err)
}

// 맵 값 확인
if value, exists := myMap["key"]; exists {
    fmt.Println("값:", value)
} else {
    fmt.Println("키가 존재하지 않음")
}

// 타입 단언
if str, ok := value.(string); ok {
    fmt.Println("문자열:", str)
} else {
    fmt.Println("문자열이 아님")
}
```

## 조건문 작성 팁

### 1. Early Return 패턴

중첩을 줄이기 위해 조건을 만족하지 않으면 빨리 반환합니다:

```go
// 나쁜 예 (중첩이 깊음)
func ProcessData(data string) {
    if data != "" {
        if len(data) > 10 {
            if isValid(data) {
                // 실제 처리
                fmt.Println("처리 완료")
            }
        }
    }
}

// 좋은 예 (early return)
func ProcessData(data string) {
    if data == "" {
        return
    }
    if len(data) <= 10 {
        return
    }
    if !isValid(data) {
        return
    }
    // 실제 처리
    fmt.Println("처리 완료")
}
```

### 2. 긍정 조건 우선

가능하면 부정 조건보다 긍정 조건을 먼저 사용합니다:

```go
// 나쁜 예
if !isEmpty {
    // 많은 코드
} else {
    // 짧은 코드
}

// 좋은 예
if isEmpty {
    // 짧은 코드
    return
}
// 많은 코드
```

### 3. 복잡한 조건은 변수로 추출

```go
// 나쁜 예
if user.Age >= 18 && user.HasPermission && !user.IsBanned && user.EmailVerified {
    // ...
}

// 좋은 예
canAccess := user.Age >= 18 && 
             user.HasPermission && 
             !user.IsBanned && 
             user.EmailVerified

if canAccess {
    // ...
}
```

## 정리
Go의 if 문은 간결하면서도 강력합니다. 특히 if 초기문은 Go만의 독특한 기능으로, 코드를 더 깔끔하게 만들어줍니다!
