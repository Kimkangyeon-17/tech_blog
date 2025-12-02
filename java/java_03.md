# Java 기초 - 조건문과 반복문

## 1. 코드 실행 흐름 제어

### 제어문이란?

Java 프로그램은 `main()` 메서드의 시작 중괄호(`{`)에서 끝 중괄호(`}`)까지 **위에서 아래로 순차적으로** 실행되는 흐름을 가집니다. 

**제어문(Control Statement)**은 이러한 실행 흐름을 개발자가 원하는 대로 제어할 수 있게 해주는 문법입니다.

### 제어문의 종류

```
제어문
├── 조건문 (특정 조건에 따라 실행)
│   ├── if 문
│   └── switch 문
└── 반복문 (특정 코드를 반복 실행)
    ├── for 문
    ├── while 문
    └── do-while 문
```

> **중첩 가능:** 제어문 블록 내부에 또 다른 제어문을 사용할 수 있습니다.

---

## 2. if 문

### 기본 if 문

if 문은 조건식의 결과에 따라 블록 실행 여부를 결정합니다.

**문법:**
```java
if (조건식) {
    // 조건식이 true일 때 실행
}
```

### 중괄호 생략 시 주의사항

```java
package ch04.sec02;

public class IfExample {
    public static void main(String[] args) {
        int score = 93;
        
        // 중괄호 있음 - 두 문장 모두 if 블록에 포함
        if (score >= 90) {
            System.out.println("점수가 90보다 큽니다.");
            System.out.println("등급은 A입니다.");
        }
        
        // 중괄호 없음 - 첫 번째 문장만 if에 포함!
        if (score < 90) 
            System.out.println("점수가 90보다 작습니다.");
            System.out.println("등급은 B입니다.");  // 항상 실행됨!
    }
}
```

**실행 결과:**
```bash
점수가 90보다 큽니다.
등급은 A입니다.
등급은 B입니다.  ← 문제! score >= 90인데 실행됨
```

> **중요:** 중괄호를 생략하면 **바로 다음 한 문장**만 if 블록에 포함됩니다. 혼란을 피하기 위해 항상 중괄호를 사용하는 것이 좋습니다!

---

## 3. if-else 문

if-else 문은 조건식의 결과에 따라 **둘 중 하나**의 블록을 실행합니다.

**문법:**
```java
if (조건식) {
    // 조건식이 true일 때 실행
} else {
    // 조건식이 false일 때 실행
}
```

```java
package ch04.sec02;

public class IfElseExample {
    public static void main(String[] args) {
        int score = 85;
        
        if (score >= 90) {
            System.out.println("점수가 90 이상입니다.");
            System.out.println("등급은 A입니다.");
        } else {
            System.out.println("점수가 90 미만입니다.");
            System.out.println("등급은 B입니다.");
        }
    }
}
```

**실행 결과:**
```bash
점수가 90 미만입니다.
등급은 B입니다.
```

---

## 4. if-else if-else 문

여러 조건을 순차적으로 검사할 때 사용합니다.

**특징:**
- else if는 **개수 제한 없음**
- **위에서 아래로** 순차적으로 조건 검사
- **첫 번째 true 조건**의 블록만 실행 후 전체 if 문 종료
- 모든 조건이 false면 else 블록 실행

```java
package ch04.sec02;

public class IfElseIfElseExample {
    public static void main(String[] args) {
        int score = 75;
        
        if (score >= 90) {
            System.out.println("점수가 90~100입니다.");
            System.out.println("등급은 A입니다.");
        } else if (score >= 80) {
            System.out.println("점수가 80~89입니다.");
            System.out.println("등급은 B입니다.");
        } else if (score >= 70) {
            System.out.println("점수가 70~79입니다.");
            System.out.println("등급은 C입니다.");
        } else {
            System.out.println("점수가 70 미만입니다.");
            System.out.println("등급은 D입니다.");
        }
    }
}
```

**실행 결과:**
```bash
점수가 70~79입니다.
등급은 C입니다.
```

### 랜덤값을 이용한 예제

```java
package ch04.sec02;

public class IfDiceExample {
    public static void main(String[] args) {
        // Math.random(): 0.0 이상 1.0 미만의 실수 반환
        // (int)(Math.random() * 6): 0~5 정수
        // (int)(Math.random() * 6) + 1: 1~6 정수
        int num = (int)(Math.random() * 6) + 1;
        
        if (num == 1) {
            System.out.println("1번이 나왔습니다.");
        } else if (num == 2) {
            System.out.println("2번이 나왔습니다.");
        } else if (num == 3) {
            System.out.println("3번이 나왔습니다.");
        } else if (num == 4) {
            System.out.println("4번이 나왔습니다.");
        } else if (num == 5) {
            System.out.println("5번이 나왔습니다.");
        } else {
            System.out.println("6번이 나왔습니다.");
        }
    }
}
```

**실행 결과:**
```bash
4번이 나왔습니다.
```

> **참고:** `Math.random()`은 0.0 ≤ 결과 < 1.0 범위의 난수를 생성합니다.

---

## 5. 중첩 if 문

if 문 내부에 또 다른 if 문을 사용할 수 있습니다.

```java
package ch04.sec02;

public class IfNestedExample {
    public static void main(String[] args) {
        int score = (int)(Math.random() * 20) + 81;  // 81~100
        System.out.println("점수: " + score);
        
        String grade;
        
        if (score >= 90) {
            if (score >= 95) {
                grade = "A+";
            } else {
                grade = "A";
            }
        } else {
            if (score >= 85) {
                grade = "B+";
            } else {
                grade = "B";
            }
        }
        
        System.out.println("학점: " + grade);
    }
}
```

**실행 결과:**
```bash
점수: 84
학점: B
```

---

## 6. switch 문

### switch 문이란?

변수의 값에 따라 실행할 코드를 선택하는 조건문입니다. 

**if-else if vs switch:**
- **if-else if**: 조건식의 결과가 true/false
- **switch**: 변수의 **값**이 일치하는 case 실행

### 기본 문법

```java
switch (변수) {
    case 값1:
        // 변수 == 값1일 때 실행
        break;
    case 값2:
        // 변수 == 값2일 때 실행
        break;
    default:
        // 일치하는 case가 없을 때 실행
}
```

### switch 문 예제

```java
package ch04.sec03;

public class SwitchExample {
    public static void main(String[] args) {
        int num = (int)(Math.random() * 6) + 1;
        
        switch (num) {
            case 1:
                System.out.println("1번이 나왔습니다.");
                break;
            case 2:
                System.out.println("2번이 나왔습니다.");
                break;
            case 3:
                System.out.println("3번이 나왔습니다.");
                break;
            case 4:
                System.out.println("4번이 나왔습니다.");
                break;
            case 5:
                System.out.println("5번이 나왔습니다.");
                break;
            default:
                System.out.println("6번이 나왔습니다.");
        }
    }
}
```

**실행 결과:**
```bash
5번이 나왔습니다.
```

### break의 중요성 (Fall-Through)

**break 문**은 switch 문을 빠져나가기 위해 필요합니다. break가 없으면 다음 case로 계속 실행됩니다. 이를 **Fall-Through**라고 합니다.

```java
package ch04.sec03;

public class SwitchNoBreakCaseExample {
    public static void main(String[] args) {
        int time = (int)(Math.random() * 4) + 8;  // 8~11
        System.out.println("[현재 시간: " + time + "시]");
        
        switch (time) {
            case 8:
                System.out.println("출근합니다.");
            case 9:
                System.out.println("회의를 합니다.");
            case 10:
                System.out.println("업무를 봅니다.");
            case 11:
                System.out.println("외근을 나갑니다.");
        }
    }
}
```

**실행 결과:**
```bash
[현재 시간: 9시]
회의를 합니다.
업무를 봅니다.
외근을 나갑니다.
```

> **Fall-Through 활용:** 의도적으로 break를 생략하여 여러 case를 연속 실행할 수 있습니다.

---

## 7. Switch Expressions (Java 13+)

### 화살표 연산자 (->)

Java 13부터는 **화살표 연산자(`->`)**를 사용한 간결한 switch 문법을 제공합니다.

**특징:**
- **break 불필요**: Fall-Through가 발생하지 않음
- 더 **간결하고 안전한** 코드

```java
package ch04.sec03;

public class SwitchExpressionsExample {
    public static void main(String[] args) {
        char grade = 'B';
        
        // 화살표 연산자 사용 (여러 줄)
        switch (grade) {
            case 'A', 'a' -> {
                System.out.println("우수 회원입니다.");
            }
            case 'B', 'b' -> {
                System.out.println("일반 회원입니다.");
            }
            default -> {
                System.out.println("손님입니다.");
            }
        }
        
        // 화살표 연산자 사용 (한 줄)
        switch (grade) {
            case 'A', 'a' -> System.out.println("우수 회원입니다.");
            case 'B', 'b' -> System.out.println("일반 회원입니다.");
            default -> System.out.println("손님입니다.");
        }
    }
}
```

**실행 결과:**
```bash
일반 회원입니다.
일반 회원입니다.
```

### switch 식으로 값 반환

switch를 **표현식(expression)**으로 사용하여 값을 직접 반환할 수 있습니다.

```java
package ch04.sec03;

public class SwitchValueExample {
    public static void main(String[] args) {
        String grade = "B";
        
        // 기존 방식 (statement)
        int score1 = 0;
        switch (grade) {
            case "A":
                score1 = 100;
                break;
            case "B":
                int result = 100 - 20;
                score1 = result;
                break;
            default:
                score1 = 60;
        }
        System.out.println("score1: " + score1);
        
        // switch 식 사용 (expression)
        int score2 = switch (grade) {
            case "A" -> 100;
            case "B" -> {
                int result = 100 - 20;
                yield result;  // 값 반환
            }
            default -> 60;
        };
        System.out.println("score2: " + score2);
    }
}
```

**실행 결과:**
```bash
score1: 80
score2: 80
```

> **yield 키워드:** 여러 줄의 블록에서 값을 반환할 때 사용합니다.

---

## 8. for 문

### for 문이란?

정해진 **횟수만큼 반복** 실행할 때 사용하는 반복문입니다.

**문법:**
```java
for (초기화식; 조건식; 증감식) {
    // 반복 실행할 코드
}
```

### for 문 실행 흐름

```
1. 초기화식 실행 (최초 1회만)
   ↓
2. 조건식 평가
   ├─ true → 3번으로
   └─ false → for 문 종료
   ↓
3. 실행문 실행
   ↓
4. 증감식 실행
   ↓
5. 2번으로 돌아가서 반복
```

### 기본 예제

```java
package ch04.sec04;

public class PrintFrom1To10Example {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            System.out.print(i + " ");
        }
    }
}
```

**실행 결과:**
```bash
1 2 3 4 5 6 7 8 9 10 
```

### 1부터 100까지 합 구하기

```java
package ch04.sec04;

public class SumFrom1To100Example {
    public static void main(String[] args) {
        int sum = 0;
        int i;
        
        for (i = 1; i <= 100; i++) {
            sum += i;
        }
        
        System.out.println("1~" + (i - 1) + " 합: " + sum);
    }
}
```

**실행 결과:**
```bash
1~100 합: 5050
```

> **변수 범위:** for 문 밖에서 `i`를 선언하면 for 문 종료 후에도 사용 가능합니다.

### 실수 카운터의 문제점

```java
package ch04.sec04;

public class FloatCounterExample {
    public static void main(String[] args) {
        for (float x = 0.1f; x <= 1.0f; x += 0.1f) {
            System.out.println(x);
        }
    }
}
```

**실행 결과:**
```bash
0.1
0.2
0.3
0.4
0.5
0.6
0.70000005  ← 오차 발생!
0.8000001
0.9000001
1.0000001
```

> **주의:** 부동소수점 연산의 정밀도 오차로 인해 **실수를 카운터 변수로 사용하는 것은 권장하지 않습니다.** 정수를 사용하고 필요시 나눗셈으로 변환하세요.

---

## 9. 중첩 for 문

for 문 내부에 또 다른 for 문을 사용할 수 있습니다.

### 구구단 출력

```java
package ch04.sec04;

public class MultiplicationTableExample {
    public static void main(String[] args) {
        for (int m = 2; m <= 9; m++) {
            System.out.println("*** " + m + "단 ***");
            for (int n = 1; n <= 9; n++) {
                System.out.println(m + " X " + n + " = " + (m * n));
            }
        }
    }
}
```

**실행 결과:**
```bash
*** 2단 ***
2 X 1 = 2
2 X 2 = 4
2 X 3 = 6
2 X 4 = 8
2 X 5 = 10
2 X 6 = 12
2 X 7 = 14
2 X 8 = 16
2 X 9 = 18
*** 3단 ***
3 X 1 = 3
3 X 2 = 6
3 X 3 = 9
...(중략)
*** 9단 ***
9 X 1 = 9
9 X 2 = 18
9 X 3 = 27
9 X 4 = 36
9 X 5 = 45
9 X 6 = 54
9 X 7 = 63
9 X 8 = 72
9 X 9 = 81
```

### 중첩 반복문 활용 - 별 찍기

```java
public class StarExample {
    public static void main(String[] args) {
        for (int i = 1; i <= 5; i++) {
            for (int j = 1; j <= i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

**실행 결과:**
```bash
*
**
***
****
*****
```

---

## 10. while 문

### while 문이란?

**조건식이 true인 동안** 반복 실행하는 반복문입니다.

**for vs while:**
- **for 문**: 반복 횟수를 알 때
- **while 문**: 반복 횟수를 모를 때 (조건에 의존)

**문법:**
```java
while (조건식) {
    // 조건식이 true일 때 반복 실행
}
```

### 기본 예제

```java
package ch04.sec05;

public class PrintFrom1To10Example {
    public static void main(String[] args) {
        int i = 1;
        while (i <= 10) {
            System.out.print(i + " ");
            i++;
        }
    }
}
```

**실행 결과:**
```bash
1 2 3 4 5 6 7 8 9 10
```

### 1부터 100까지 합 구하기

```java
package ch04.sec05;

public class SumFrom1To100Example {
    public static void main(String[] args) {
        int sum = 0;
        int i = 1;
        
        while (i <= 100) {
            sum += i;
            i++;
        }
        
        System.out.println("1~" + (i - 1) + " 합: " + sum);
    }
}
```

**실행 결과:**
```bash
1~100 합: 5050
```

### 사용자 입력 제어

```java
package ch04.sec05;

import java.util.Scanner;

public class KeyControlExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean run = true;
        int speed = 0;
        
        while (run) {
            System.out.println("----------");
            System.out.println("1.증속 | 2.감속 | 3.중지");
            System.out.println("----------");
            System.out.print("선택: ");
            
            String strNum = scanner.nextLine();
            
            if (strNum.equals("1")) {
                speed++;
                System.out.println("현재 속도 = " + speed);
            } else if (strNum.equals("2")) {
                speed--;
                System.out.println("현재 속도 = " + speed);
            } else if (strNum.equals("3")) {
                run = false;
            }
        }
        
        System.out.println("프로그램 종료");
        scanner.close();
    }
}
```

**실행 결과:**
```bash
----------
1.증속 | 2.감속 | 3.중지
----------
선택: 1
현재 속도 = 1
----------
1.증속 | 2.감속 | 3.중지
----------
선택: 1
현재 속도 = 2
----------
1.증속 | 2.감속 | 3.중지
----------
선택: 2
현재 속도 = 1
----------
1.증속 | 2.감속 | 3.중지
----------
선택: 3
프로그램 종료
```

---

## 11. do-while 문

### do-while 문이란?

**블록을 먼저 실행**한 후 조건식을 검사하는 반복문입니다.

**while vs do-while:**
- **while**: 조건을 먼저 검사 → 0번 이상 실행
- **do-while**: 블록을 먼저 실행 → **최소 1번 이상** 실행

**문법:**
```java
do {
    // 최소 1번은 실행
} while (조건식);
```

### 실행 흐름 비교

```java
// while: 조건이 false면 한 번도 실행 안 됨
int i = 10;
while (i < 5) {
    System.out.println(i);  // 실행 안 됨
    i++;
}

// do-while: 조건이 false여도 1번은 실행됨
int j = 10;
do {
    System.out.println(j);  // 1번 실행
    j++;
} while (j < 5);
```

### 메시지 입력 예제

```java
package ch04.sec06;

import java.util.Scanner;

public class DoWhileExample {
    public static void main(String[] args) {
        System.out.println("메시지를 입력하세요");
        System.out.println("프로그램을 종료하려면 q를 입력하세요.");
        
        Scanner scanner = new Scanner(System.in);
        String inputString;
        
        do {
            System.out.print("> ");
            inputString = scanner.nextLine();
            System.out.println(inputString);
        } while (!inputString.equals("q"));
        
        System.out.println();
        System.out.println("프로그램 종료");
        scanner.close();
    }
}
```

**실행 결과:**
```bash
메시지를 입력하세요
프로그램을 종료하려면 q를 입력하세요.
> 안녕하세요.
안녕하세요.
> 반갑습니다.
반갑습니다.
> q
q

프로그램 종료
```

---

## 12. break 문

### break 문이란?

**반복문을 즉시 종료**하고 반복문 밖으로 빠져나가는 제어문입니다.

**사용 가능한 곳:**
- for 문
- while 문
- do-while 문
- switch 문

### 기본 예제

```java
package ch04.sec07;

public class BreakExample {
    public static void main(String[] args) {
        while (true) {  // 무한 루프
            int num = (int)(Math.random() * 6) + 1;
            System.out.println(num);
            if (num == 6) {
                break;  // 6이 나오면 while 문 종료
            }
        }
        System.out.println("프로그램 종료");
    }
}
```

**실행 결과:**
```bash
2
1
4
4
5
5
6
프로그램 종료
```

---

## 13. 레이블을 이용한 break

### 레이블(Label)이란?

중첩된 반복문에서 **바깥쪽 반복문까지 한 번에 빠져나갈 때** 사용합니다.

**문법:**
```java
레이블명:
for (...) {
    for (...) {
        break 레이블명;  // 레이블이 붙은 반복문 종료
    }
}
```

```java
package ch04.sec07;

public class BreakOutterExample {
    public static void main(String[] args) {
        Outter: for (char upper = 'A'; upper <= 'Z'; upper++) {
            for (char lower = 'a'; lower <= 'z'; lower++) {
                System.out.println(upper + "-" + lower);
                if (lower == 'g') {
                    break Outter;  // 바깥쪽 for 문 종료
                }
            }
        }
        System.out.println("프로그램 실행 종료");
    }
}
```

**실행 결과:**
```bash
A-a
A-b
A-c
A-d
A-e
A-f
A-g
프로그램 실행 종료
```

### 레이블 없이 사용한 경우 비교

```java
// 레이블 없으면 안쪽 for만 종료
for (char upper = 'A'; upper <= 'Z'; upper++) {
    for (char lower = 'a'; lower <= 'z'; lower++) {
        System.out.println(upper + "-" + lower);
        if (lower == 'g') {
            break;  // 안쪽 for만 종료, 바깥쪽 for는 계속
        }
    }
}
```

**실행 결과:**
```bash
A-a
A-b
...
A-g
B-a  ← 바깥쪽 for는 계속 실행됨
B-b
...
```

---

## 14. continue 문

### continue 문이란?

현재 반복을 중단하고 **다음 반복으로 건너뛰는** 제어문입니다.

**동작 방식:**
- **for 문**: 증감식으로 이동
- **while/do-while 문**: 조건식으로 이동

### for 문에서 continue

```java
package ch04.sec08;

public class ContinueExample {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            if (i % 2 != 0) {  // 홀수면
                continue;      // 다음 반복으로 (증감식으로)
            }
            System.out.print(i + " ");  // 짝수만 출력
        }
    }
}
```

**실행 결과:**
```bash
2 4 6 8 10 
```

### while 문에서 continue

```java
public class ContinueWhileExample {
    public static void main(String[] args) {
        int i = 0;
        while (i < 10) {
            i++;
            if (i % 2 != 0) {  // 홀수면
                continue;      // 다음 반복으로 (조건식으로)
            }
            System.out.print(i + " ");
        }
    }
}
```

**실행 결과:**
```bash
2 4 6 8 10 
```

> **주의:** while 문에서 continue 사용 시 증감식을 continue 전에 작성해야 무한루프를 방지할 수 있습니다!

### continue 실행 흐름

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue;  // i++로 이동
    }
    System.out.println(i);
}
// 출력: 1 2 4 5 (3은 출력 안 됨)
```

---

## 정리

### 조건문 비교

| 구분 | if-else if-else | switch |
|------|----------------|--------|
| **사용 시점** | 조건식 (범위, 관계 연산) | 값의 일치 확인 |
| **조건 개수** | 제한 없음 | case 수만큼 |
| **가독성** | 복잡한 조건 가능 | 간단하고 명확 |
| **Fall-Through** | 없음 | break 없으면 발생 |

### 반복문 비교

| 구분 | for | while | do-while |
|------|-----|-------|----------|
| **반복 조건** | 횟수 기반 | 조건 기반 | 조건 기반 |
| **초기화/증감** | 구조 내 포함 | 별도 작성 | 별도 작성 |
| **최소 실행** | 0회 | 0회 | **1회** |
| **사용 시점** | 반복 횟수 알 때 | 조건 기반 반복 | 최소 1번 실행 필요 |

### 제어문 활용 팁

1. **for vs while 선택**
   - 반복 횟수를 알 때 → for 문
   - 조건에 따라 반복 → while 문

2. **무한 루프**
   ```java
   while (true) { ... }    // 주로 사용
   for (;;) { ... }        // 가능하지만 덜 직관적
   ```

3. **중첩 반복문**
   - 가독성을 위해 **3중 중첩 이상은 지양**
   - 복잡한 경우 메서드로 분리

4. **break vs continue**
   - break: 반복문 완전 종료
   - continue: 현재 반복만 건너뛰기

5. **switch 문**
   - Java 13+ 에서는 화살표 연산자 사용 권장
   - break 누락 주의 (Fall-Through 의도가 아니면)

### 주의사항

- **if 문 중괄호**: 생략 시 한 문장만 포함되므로 항상 사용 권장
- **실수 카운터**: 부동소수점 오차로 for 문에서 사용 지양
- **무한 루프**: break 조건 반드시 포함
- **while 문 continue**: 증감식을 continue 전에 작성

---
