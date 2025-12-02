# Go 언어 for 문

Go에는 반복문이 **오직 for 문 하나만** 존재합니다. while, do-while 같은 다른 반복문은 없습니다!

## for 문 동작 원리

### 기본 형태

```go
for 초기문; 조건문; 후처리 {
    코드 블록  // 조건문이 true인 경우 수행
}
```

**동작 순서:**
1. 초기문 실행 (최초 1회만)
2. 조건문 확인
3. 조건이 true면 코드 블록 실행
4. 후처리 실행
5. 2번으로 돌아가서 반복

### 기본 for 문 실습

```go
package main

import "fmt"

func main() {
	for i := 0; i < 10; i++ {  // 초기문; 조건문; 후처리
		fmt.Print(i, ", ")      // i 값을 출력합니다.
	}
	// fmt.Println(i)  // 에러! i는 for 블록 밖에서 사용 불가
}
```

실행 결과:

```bash
0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 
```

**변수 스코프:**
- 초기문에서 선언된 변수 `i`는 for 블록 안에서만 사용 가능
- for 문이 끝나면 `i`는 사라집니다

## for 문의 다양한 형태

Go의 for 문은 매우 유연합니다. 상황에 따라 다양한 형태로 사용할 수 있습니다.

### 1. 초기문 생략

```go
package main

import "fmt"

func main() {
	i := 0  // 외부에서 선언
	for ; i < 5; i++ {
		fmt.Println(i)
	}
	fmt.Println("최종 i:", i)  // i는 외부에서 접근 가능
}
```

실행 결과:

```bash
0
1
2
3
4
최종 i: 5
```

### 2. 후처리 생략

```go
package main

import "fmt"

func main() {
	for i := 0; i < 5; {
		fmt.Println(i)
		i++  // 후처리를 블록 안에서 직접 처리
	}
}
```

### 3. 조건문만 있는 경우 (while 문처럼 사용)

```go
package main

import "fmt"

func main() {
	i := 0
	for i < 5 {  // 세미콜론 생략 가능
		fmt.Println(i)
		i++
	}
}
```

이 형태는 다른 언어의 **while 문**과 동일합니다!

### 4. 무한 루프

조건문을 생략하면 무한 루프가 됩니다.

```go
for {
    코드 블록  // 영원히 반복
}
```

**무한 루프 예제:**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	i := 1
	for {
		time.Sleep(time.Second)  // 1초 대기
		fmt.Println(i)
		i++
	}
}
```

실행 결과:

```bash
1
2
3
4
... (계속 반복)
```

**주의사항:**
- 프로그램을 종료하거나 `break`로 빠져나오지 않으면 계속 실행됩니다
- 무한 루프를 사용할 때는 반드시 탈출 조건을 고려해야 합니다

### 5. range로 정수 순회 (Go 1.22+)

Go 1.22부터는 range로 정수를 직접 순회할 수 있습니다:

```go
package main

import "fmt"

func main() {
	for i := range 10 {  // 0부터 9까지
		fmt.Println(i)
	}
}
```

실행 결과:

```bash
0
1
2
3
4
5
6
7
8
9
```

**특징:**
- `for i := range n`은 0부터 n-1까지 반복
- `for i := 0; i < n; i++`와 동일한 효과

## continue와 break

반복문의 흐름을 제어하는 두 가지 키워드입니다.

### continue

현재 반복을 건너뛰고 다음 반복으로 진행합니다.

### break

반복문을 즉시 종료하고 빠져나갑니다.

### continue와 break 실습

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	stdin := bufio.NewReader(os.Stdin)
	for {
		fmt.Println("입력하세요.")
		var number int
		_, err := fmt.Scanln(&number)
		if err != nil {
			fmt.Println("숫자를 입력하세요.")
			stdin.ReadString('\n')
			continue  // 에러 발생 시 다음 반복으로
		}
		fmt.Printf("입력하신 숫자는 %d입니다.\n", number)
		if number%2 == 0 {
			break  // 짝수면 for 문 종료
		}
	}
	fmt.Println("for문이 종료됐습니다.")
}
```

실행 예시:

```bash
입력하세요.
abc
숫자를 입력하세요.
입력하세요.
11
입력하신 숫자는 11입니다.
입력하세요.
12
입력하신 숫자는 12입니다.
for문이 종료됐습니다.
```

**동작 방식:**
1. 숫자가 아닌 입력 → `continue`로 다시 입력 요청
2. 홀수 입력 → 출력 후 다시 입력 요청
3. 짝수 입력 → `break`로 for 문 종료

### continue와 break 비교

```go
// continue 예시: 홀수만 출력
for i := 0; i < 10; i++ {
	if i%2 == 0 {
		continue  // 짝수는 건너뜀
	}
	fmt.Println(i)  // 홀수만 출력: 1, 3, 5, 7, 9
}

// break 예시: 5까지만 출력
for i := 0; i < 10; i++ {
	if i == 5 {
		break  // 5가 되면 종료
	}
	fmt.Println(i)  // 0, 1, 2, 3, 4만 출력
}
```

## 중첩 for 문

for 문 안에 또 다른 for 문을 넣을 수 있습니다.

### 별 찍기 패턴

```go
package main

import "fmt"

func main() {
	for i := 0; i < 5; i++ {        // 5번 반복
		for j := 0; j < i+1; j++ {  // 현재 i값 + 1만큼 반복
			fmt.Print("*")           // *을 출력
		}
		fmt.Println()                // 줄 바꿈
	}
}
```

실행 결과:

```bash
*
**
***
****
*****
```

**동작 방식:**
- i=0: j는 0~0 (1개 별)
- i=1: j는 0~1 (2개 별)
- i=2: j는 0~2 (3개 별)
- ...

### 구구단 출력

```go
package main

import "fmt"

func main() {
	for dan := 2; dan <= 9; dan++ {
		fmt.Printf("==== %d단 ====\n", dan)
		for num := 1; num <= 9; num++ {
			fmt.Printf("%d * %d = %d\n", dan, num, dan*num)
		}
		fmt.Println()
	}
}
```

실행 결과:

```bash
==== 2단 ====
2 * 1 = 2
2 * 2 = 4
...
2 * 9 = 18

==== 3단 ====
3 * 1 = 3
...
```

## 중첩 for 문과 break

중첩된 for 문에서 break를 사용할 때 주의가 필요합니다.

### 문제 상황: 플래그 변수 사용

```go
package main

import "fmt"

func main() {
	a := 1
	b := 1
	found := false

	for ; a <= 9; a++ {
		for b = 1; b <= 9; b++ {
			if a*b == 45 {
				found = true
				break  // 안쪽 for만 빠져나옴
			}
		}
		if found {
			break  // 바깥쪽 for를 빠져나옴
		}
	}
	fmt.Printf("%d * %d = %d\n", a, b, a*b)
}
```

실행 결과:

```bash
5 * 9 = 45
```

**문제점:**
- 플래그 변수(`found`)가 필요함
- 코드가 복잡해짐

### 해결 방법 1: 레이블 (Label) 사용

```go
package main

import "fmt"

func main() {
	a := 1
	b := 1

OuterFor:  // 레이블 정의
	for ; a <= 9; a++ {
		for b = 1; b <= 9; b++ {
			if a*b == 45 {
				break OuterFor  // 레이블이 붙은 for 문을 빠져나옴
			}
		}
	}
	fmt.Printf("%d * %d = %d\n", a, b, a*b)
}
```

실행 결과:

```bash
5 * 9 = 45
```

**레이블의 장점:**
- 플래그 변수 불필요
- 코드가 간결해짐
- 의도가 명확함

**레이블 규칙:**
- 레이블 이름은 대소문자를 구분합니다
- 레이블 뒤에 콜론(`:`)을 붙입니다
- `break`와 `continue`에서 사용 가능

### 해결 방법 2: 함수로 분리

```go
package main

import "fmt"

func find45(a int) (int, bool) {
	for b := 1; b <= 9; b++ {
		if a*b == 45 {
			return b, true  // 찾으면 즉시 반환
		}
	}
	return 0, false  // 못 찾으면 false 반환
}

func main() {
	a := 1
	b := 0

	for ; a <= 9; a++ {
		var found bool
		if b, found = find45(a); found {
			break
		}
	}
	fmt.Printf("%d * %d = %d\n", a, b, a*b)
}
```

실행 결과:

```bash
5 * 9 = 45
```

**함수 분리의 장점:**
- 코드의 재사용성 향상
- 각 함수가 하나의 역할만 수행
- 테스트가 쉬워짐
- 가독성 향상

### 세 가지 방법 비교

| 방법 | 장점 | 단점 |
|------|------|------|
| 플래그 변수 | 이해하기 쉬움 | 코드가 복잡해짐 |
| 레이블 | 간결함 | 남용 시 가독성 저하 |
| 함수 분리 | 재사용 가능, 테스트 용이 | 간단한 경우 과할 수 있음 |

## for 문 활용 팁

### 1. 무한 루프의 안전한 사용

```go
// 서버 프로그램의 일반적인 패턴
for {
	select {
	case msg := <-messageChan:
		processMessage(msg)
	case <-stopChan:
		return  // 종료 신호를 받으면 안전하게 종료
	}
}
```

### 2. 역순 반복

```go
// 거꾸로 세기
for i := 10; i > 0; i-- {
	fmt.Println(i)
}
fmt.Println("발사!")
```

### 3. 여러 변수 동시 제어

```go
// 피보나치 수열 생성
for a, b := 0, 1; a < 100; a, b = b, a+b {
	fmt.Print(a, " ")
}
```

실행 결과:

```bash
0 1 1 2 3 5 8 13 21 34 55 89
```

### 4. 조건 없이 반복 횟수만 지정

```go
// 정확히 10번 실행
for i := 0; i < 10; i++ {
	doSomething()
}

// 값이 필요 없으면 _ 사용
for _ = range 10 {
	doSomething()
}
```

## 흔한 실수와 해결 방법

### 1. 무한 루프에 빠지기

```go
// 잘못된 예
for i := 0; i < 10; {
	fmt.Println(i)
	// i++를 까먹음! 무한 루프
}

// 올바른 예
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
```

### 2. 변수 스코프 실수

```go
// 잘못된 예
for i := 0; i < 5; i++ {
	// ...
}
fmt.Println(i)  // 에러! i는 for 블록 밖에서 사용 불가

// 올바른 예
i := 0
for ; i < 5; i++ {
	// ...
}
fmt.Println(i)  // OK
```

### 3. 중첩 for에서 break 실수

```go
// 의도: 두 for 문 모두 빠져나가기
// 실제: 안쪽 for 문만 빠져나감
for i := 0; i < 10; i++ {
	for j := 0; j < 10; j++ {
		if i*j == 20 {
			break  // 안쪽 for만 종료
		}
	}
}

// 해결: 레이블 사용
Outer:
for i := 0; i < 10; i++ {
	for j := 0; j < 10; j++ {
		if i*j == 20 {
			break Outer  // 바깥쪽 for도 종료
		}
	}
}
```

## 정리
Go의 for 문은 하나이지만 매우 유연합니다. while, do-while 등의 기능을 모두 for 문으로 구현할 수 있습니다!