# fmt 패키지를 이용한 텍스트 입출력

Go 언어에서 가장 기본적이고 자주 사용하는 fmt 패키지에 대해 알아보겠습니다.

## 표준 출력 함수

fmt 패키지는 세 가지 주요 출력 함수를 제공합니다:

- **Print()**: 함수 입력값들을 출력합니다.
- **Println()**: 함수 입력값들을 출력하고 자동으로 개행(줄바꿈)합니다.
- **Printf()**: 서식(format)에 맞도록 입력값들을 출력합니다.

### 기본 출력 실습

```go
package main

import "fmt"

func main() {
	var a int = 10
	var b int = 20
	var f float64 = 3279943.8297

	fmt.Print("a:", a, " b:", b, "\n")
	fmt.Println("a:", a, "b:", b, "f:", f)
	fmt.Printf("a: %d b: %d f: %f\n", a, b, f)
}
```

위 코드를 build 후 실행하면:

```bash
a:10 b:20
a: 10 b: 20 f: 3.2799438297e+06
a: 10 b: 20 f: 3279943.829700
```

**함수별 차이점:**
- `Print()`: 띄어쓰기 없이 연속으로 출력, 개행 없음
- `Println()`: 값 사이에 자동으로 띄어쓰기, 자동 개행
- `Printf()`: 서식 지정자(%d, %f 등)를 사용하여 형식을 지정

## 출력 서식 (Format Specifiers)

Printf()에서 사용할 수 있는 다양한 서식 지정자입니다:

### 일반 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %v | 데이터 타입에 맞춰서 기본 형태로 출력 | 123, "hello" |
| %T | 데이터 타입을 출력 | int, string |
| %% | % 기호 자체를 출력 | % |

### 불리언 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %t | 불리언을 true/false로 출력 | true, false |

### 정수 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %d | 10진수 정수값으로 출력 | 123 |
| %b | 2진수로 출력 | 1111011 |
| %c | 유니코드 문자를 출력 | A |
| %o | 8진수로 출력 | 173 |
| %O | 8진수임을 표시하는 0o를 붙여서 출력 | 0o173 |
| %x | 16진수로 출력 (소문자 a~f) | 7b |
| %X | 16진수로 출력 (대문자 A~F) | 7B |

### 실수 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %f, %F | 실수를 소수점 형태로 출력 (기본 6자리) | 123.456000 |
| %e, %E | 지수 형태로 실수를 출력 | 1.234560e+02 |
| %g, %G | 값에 따라 %f 또는 %e 형태로 자동 선택 | 123.456 또는 1.23e+10 |

### 문자열 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %s | 문자열을 출력 | hello |
| %q | 큰따옴표로 감싸서 문자열 출력 | "hello" |

### 포인터 서식

| 서식 | 설명 | 예시 |
| ---- | ---- | ---- |
| %p | 메모리 주솟값을 출력 | 0xc000010230 |

## 서식 폭(Width)과 정렬

숫자 출력 시 자릿수를 지정하고 정렬할 수 있습니다.

### 서식 폭 실습

```go
package main

import "fmt"

func main() {
	var a = 123
	var b = 456
	var c = 123456789 

	fmt.Printf("%5d, %5d\n", a, b)      // 5칸 확보, 오른쪽 정렬
	fmt.Printf("%05d, %05d\n", a, b)    // 5칸 확보, 빈칸을 0으로 채움
	fmt.Printf("%-5d, %-05d\n", a, b)   // 5칸 확보, 왼쪽 정렬

	fmt.Printf("%5d, %5d\n", c, c)
	fmt.Printf("%05d, %05d\n", c, c)
	fmt.Printf("%-5d, %-05d\n", c, c)
}
```

위 코드를 build 후 실행하면:

```bash
  123,   456
00123, 00456
123  , 456  
123456789, 123456789
123456789, 123456789
123456789, 123456789
```

**서식 폭 설명:**
- `%5d`: 5칸을 확보하고 오른쪽 정렬 (기본값)
- `%05d`: 5칸을 확보하고 빈칸을 0으로 채움
- `%-5d`: 5칸을 확보하고 왼쪽 정렬
- 값이 지정한 칸 수보다 크면 그대로 출력됩니다

### 실수 서식 폭

```go
package main

import "fmt"

func main() {
	var f float64 = 123.456789

	fmt.Printf("%f\n", f)        // 기본 (소수점 6자리)
	fmt.Printf("%.2f\n", f)      // 소수점 2자리
	fmt.Printf("%10.2f\n", f)    // 전체 10칸, 소수점 2자리
	fmt.Printf("%010.2f\n", f)   // 전체 10칸, 소수점 2자리, 0으로 채움
}
```

실행 결과:

```bash
123.456789
123.46
    123.46
0000123.46
```

## 특수 문자 (Escape Sequences)

문자열 내에서 특별한 기능을 수행하는 문자들입니다:

| 특수문자 | 설명 | 예시 |
| ---- | ---- | ---- |
| \n | 줄바꿈 (개행) | "Hello\nWorld" |
| \t | 탭 (4칸 또는 8칸 띄어쓰기) | "Name:\tJohn" |
| \\ | 백슬래시 자체를 출력 | "C:\\Users\\Go" |
| \" | 큰따옴표를 출력 | "He said \"Hi\"" |
| \' | 작은따옴표를 출력 | 'It\'s mine' |

### 특수 문자 실습

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello\nWorld")           // 줄바꿈
	fmt.Println("Name:\tJohn")            // 탭
	fmt.Println("Path: C:\\Users\\Go")    // 백슬래시
	fmt.Println("He said \"Hello\"")      // 큰따옴표
}
```

실행 결과:

```bash
Hello
World
Name:	John
Path: C:\Users\Go
He said "Hello"
```

## 표준 입력 함수

사용자로부터 입력을 받는 함수들입니다:

- **Scan()**: 표준 입력에서 값을 입력받습니다 (공백이나 개행으로 구분)
- **Scanf()**: 표준 입력에서 서식 형태로 값을 입력받습니다
- **Scanln()**: 표준 입력에서 한 줄을 읽어서 값을 입력받습니다 (개행까지만)

### 기본 입력 실습

```go
package main

import "fmt"

func main() {
	var a int
	var b int

	n, err := fmt.Scanln(&a, &b)
	if err != nil {
		fmt.Println("입력 오류:", err)
	} else {
		fmt.Printf("입력된 값의 개수: %d, a: %d, b: %d\n", n, a, b)
	}
}
```

실행 후 입력:

```bash
10 20
입력된 값의 개수: 2, a: 10, b: 20
```

**주의사항:**
- `&a`, `&b`처럼 변수 앞에 `&`를 붙여야 합니다 (포인터로 전달)
- 입력값이 부족하거나 타입이 맞지 않으면 에러가 발생합니다
- `Scanln()`은 개행(Enter)이 입력되면 종료됩니다

### 다양한 입력 오류 케이스

```go
package main

import "fmt"

func main() {
	var a int
	var b int

	fmt.Print("두 개의 정수를 입력하세요: ")
	n, err := fmt.Scanln(&a, &b)
	
	if err != nil {
		fmt.Println("입력 오류:", err)
	} else {
		fmt.Printf("성공! 입력된 개수: %d, 값: %d, %d\n", n, a, b)
	}
}
```

**오류가 발생하는 경우:**
- 숫자가 아닌 문자 입력: `abc def`
- 입력 개수가 부족: `10` (하나만 입력)
- 입력 개수가 초과: `10 20 30` (세 개 입력)

## 입력 버퍼

입력 버퍼는 입력된 값을 임시로 저장해두는 공간입니다. 입력 오류가 발생하면 잘못된 입력이 버퍼에 남아있어 다음 입력에 영향을 줄 수 있습니다.

### 입력 버퍼 처리 실습

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	stdin := bufio.NewReader(os.Stdin)

	var a int
	var b int

	// 첫 번째 입력 시도
	fmt.Print("첫 번째 입력 (두 정수): ")
	n, err := fmt.Scanln(&a, &b)
	if err != nil {
		fmt.Println("입력 오류:", err)
		stdin.ReadString('\n') // 버퍼를 비워줌
	} else {
		fmt.Printf("입력 성공: %d개, 값: %d, %d\n", n, a, b)
	}

	// 두 번째 입력 시도
	fmt.Print("두 번째 입력 (두 정수): ")
	n, err = fmt.Scanln(&a, &b)
	if err != nil {
		fmt.Println("입력 오류:", err)
	} else {
		fmt.Printf("입력 성공: %d개, 값: %d, %d\n", n, a, b)
	}
}
```

**코드 설명:**
- `bufio.NewReader(os.Stdin)`: 표준 입력을 읽는 Reader를 생성
- `stdin.ReadString('\n')`: 개행 문자까지 버퍼를 읽어서 비움
- 첫 번째 입력에서 오류가 발생해도 버퍼를 비워서 두 번째 입력이 정상 동작

### 실행 예시

```bash
첫 번째 입력 (두 정수): abc
입력 오류: expected integer
두 번째 입력 (두 정수): 10 20
입력 성공: 2개, 값: 10, 20
```

버퍼를 비우지 않으면 첫 번째 오류가 두 번째 입력에도 영향을 미칩니다!

## 실전 활용 예제

### 사용자 정보 입력받기

```go
package main

import "fmt"

func main() {
	var name string
	var age int
	var height float64

	fmt.Print("이름을 입력하세요: ")
	fmt.Scanln(&name)

	fmt.Print("나이를 입력하세요: ")
	fmt.Scanln(&age)

	fmt.Print("키를 입력하세요(cm): ")
	fmt.Scanln(&height)

	fmt.Println("\n===== 입력된 정보 =====")
	fmt.Printf("이름: %s\n", name)
	fmt.Printf("나이: %d세\n", age)
	fmt.Printf("키: %.1fcm\n", height)
}
```

### 서식을 이용한 표 출력

```go
package main

import "fmt"

func main() {
	fmt.Println("===== 성적표 =====")
	fmt.Printf("%-10s %5s %5s %5s\n", "이름", "국어", "영어", "수학")
	fmt.Println("--------------------------------")
	fmt.Printf("%-10s %5d %5d %5d\n", "김철수", 85, 90, 88)
	fmt.Printf("%-10s %5d %5d %5d\n", "이영희", 92, 88, 95)
	fmt.Printf("%-10s %5d %5d %5d\n", "박민수", 78, 85, 80)
}
```

실행 결과:

```bash
===== 성적표 =====
이름            국어    영어    수학
--------------------------------
김철수            85    90    88
이영희            92    88    95
박민수            78    85    80
```

## 정리
fmt 패키지는 Go 프로그래밍에서 가장 기본이 되는 입출력 도구입니다. 다양한 서식 지정자와 옵션을 잘 활용하면 깔끔하고 읽기 쉬운 출력을 만들 수 있습니다.