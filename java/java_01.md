# Java 기초 - 변수와 타입

## 1. 변수 선언

### 네이밍 규칙
- Java에서는 **캐멀 케이스(Camel Case)** 사용
  - 변수명: `userName`, `totalScore`
  - 클래스명: `Calculator`, `StudentInfo` (첫 글자 대문자)

### 선언 방법

```java
package sec12;

public class Calculator {
    public static void main(String[] args) {
        // 선언 후 초기화
        int age;
        age = 15;
        System.out.println(age);

        // 선언과 동시에 초기화
        int score = 90;
        System.out.println(score);
    }
}
```

**실행 결과:**
```bash
15
90
```

### 변수 활용 예시

```java
package ch02.sec01;

public class VariableUseExample {
    public static void main(String[] args) {
        int hour = 3;
        int minute = 5;
        System.out.println(hour + "시간 " + minute + "분");
        
        int totalMinute = (hour * 60) + minute;
        System.out.println("총 " + totalMinute + "분");
    }
}
```

**실행 결과:**
```bash
3시간 5분
총 185분
```

### 변수값 교환

```java
package ch02.sec01;

public class VariableExchangeExample {
    public static void main(String[] args) {
        int x = 3;
        int y = 5;
        
        System.out.println("x:" + x + ", y:" + y);

        // 임시 변수를 이용한 값 교환
        int temp = x;
        x = y;
        y = temp;

        System.out.println("x:" + x + ", y:" + y);
    }
}
```

**실행 결과:**
```bash
x:3, y:5
x:5, y:3
```

---

## 2. 정수 타입

### 정수 타입의 종류

| 타입 | 메모리 크기 | 표현 범위 |
|------|-------------|-----------|
| byte | 1byte (8bit) | -128 ~ 127 |
| short | 2byte (16bit) | -32,768 ~ 32,767 |
| char | 2byte (16bit) | 0 ~ 65,535 (유니코드) |
| int | 4byte (32bit) | -2,147,483,648 ~ 2,147,483,647 |
| long | 8byte (64bit) | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 |

### 2진수 표현 방식

정수는 **2의 보수** 방식으로 저장됩니다.
- 최상위 비트가 0이면 양수, 1이면 음수

**byte 타입 예시 (8bit):**
```
01111111 = 127
01111110 = 126
...
00000001 = 1
00000000 = 0
11111111 = -1
11111110 = -2
...
10000001 = -127
10000000 = -128
```

### 정수 리터럴 표현

```java
package ch02.sec02;

public class IntegerLiteralExample {
    public static void main(String[] args) {
        int var1 = 0b1011;      // 2진수
        int var2 = 0206;        // 8진수
        int var3 = 365;         // 10진수
        int var4 = 0xB3;        // 16진수
        
        System.out.println("var1: " + var1);
        System.out.println("var2: " + var2);
        System.out.println("var3: " + var3);
        System.out.println("var4: " + var4);
    }
}
```

**실행 결과:**
```bash
var1: 11
var2: 134
var3: 365
var4: 179
```

### long 타입 사용

```java
package ch02.sec02;

public class LongExample {
    public static void main(String[] args) {
        long var1 = 10;
        long var2 = 20L;
        // long var3 = 1000000000000;  // 컴파일 에러! int 범위 초과
        long var4 = 1000000000000L;    // L 접미사 필수
        
        System.out.println(var1);
        System.out.println(var2);
        System.out.println(var4);
    }
}
```

**실행 결과:**
```bash
10
20
1000000000000
```

> **주의:** int 범위를 벗어나는 정수 리터럴은 반드시 `L` 또는 `l` 접미사를 붙여야 합니다.

---

## 3. 문자 타입

### char 타입의 특징

- **유니코드(Unicode)** 방식으로 문자를 저장 (ASCII 코드의 확장)
- 2바이트 크기로 다국어 지원
- 내부적으로는 정수값으로 저장

```java
char var1 = 'A';        // 문자 리터럴
char var2 = 65;         // 10진수
char var3 = 0x0041;     // 16진수

// 모두 'A'로 출력됨
```

### 문자 타입 예시

```java
package ch02.sec03;

public class CharExample {
    public static void main(String[] args) {
        char c1 = 'A';
        char c2 = 65;
        
        char c3 = '가';
        char c4 = 44032;
        
        System.out.println(c1);
        System.out.println(c2);
        System.out.println(c3);
        System.out.println(c4);
    }
}
```

**실행 결과:**
```bash
A
A
가
가
```

---

## 4. 실수 타입

### 실수 타입의 종류

| 타입 | 메모리 크기 | 유효 소수점 이하 자리 | 기본 타입 |
|------|-------------|---------------------|----------|
| float | 4byte (32bit) | 약 7자리 | ❌ |
| double | 8byte (64bit) | 약 15자리 | ✅ |

> Java에서 실수 리터럴은 **기본적으로 double 타입**입니다.

### 부동소수점 방식

실수는 **부동소수점(Floating Point)** 방식으로 저장됩니다.

**구조:**
- **float**: 부호(1bit) + 지수(8bit) + 가수(23bit) = 32bit
- **double**: 부호(1bit) + 지수(11bit) + 가수(52bit) = 64bit

### 실수 타입 예시

```java
package ch02.sec04;

public class FloatDoubleExample {
    public static void main(String[] args) {
        // 정밀도 확인
        float var1 = 0.123456789123456789f;
        double var2 = 0.123456789123456789;
        
        System.out.println("var1: " + var1);
        System.out.println("var2: " + var2);
        
        // 과학적 표기법 (e 표기법)
        double var3 = 3e6;      // 3 × 10^6
        float var4 = 3e6F;
        double var5 = 2e-3;     // 2 × 10^-3
        
        System.out.println("var3: " + var3);
        System.out.println("var4: " + var4);
        System.out.println("var5: " + var5);
    }
}
```

**실행 결과:**
```bash
var1: 0.12345679
var2: 0.12345678901234568
var3: 3000000.0
var4: 3000000.0
var5: 0.002
```

> **float 타입** 사용 시 리터럴 뒤에 `f` 또는 `F` 접미사를 붙여야 합니다.

---

## 5. 논리 타입

### boolean 타입

- `true` 또는 `false` 값만 가질 수 있음
- 조건문과 반복문에서 주로 사용

```java
package ch02.sec05;

public class BooleanExample {
    public static void main(String[] args) {
        boolean stop = true;
        
        if(stop) {
            System.out.println("중지합니다.");
        } else {
            System.out.println("시작합니다.");
        }
        
        int x = 10;
        boolean result1 = (x == 20);
        boolean result2 = (x != 20);
        
        System.out.println("result1: " + result1);
        System.out.println("result2: " + result2);
    }
}
```

**실행 결과:**
```bash
중지합니다.
result1: false
result2: true
```

---

## 6. 문자열 타입

### String 타입

- 작은 따옴표(`'`)는 **char** 타입 (한 문자)
- 큰 따옴표(`"`)는 **String** 타입 (문자열)
- String은 **참조 타입**입니다

### 이스케이프 문자

| 이스케이프 문자 | 의미 |
|---------------|------|
| `\"` | 큰 따옴표 |
| `\'` | 작은 따옴표 |
| `\\` | 역슬래시 |
| `\t` | 탭 |
| `\n` | 줄바꿈 |
| `\r` | 캐리지 리턴 |

```java
package ch02.sec06;

public class StringExample {
    public static void main(String[] args) {
        String name = "홍길동";
        String job = "프로그래머";
        
        System.out.println(name);
        System.out.println(job);
        
        String str = "나는 \"자바\"를 배웁니다.";
        System.out.println(str);
        
        str = "번호\t이름\t직업";
        System.out.println(str);
        
        System.out.print("나는\n");
        System.out.print("자바를\n");
        System.out.print("배웁니다.");
    }
}
```

**실행 결과:**
```bash
홍길동
프로그래머
나는 "자바"를 배웁니다.
번호	이름	직업
나는
자바를
배웁니다.
```

### 텍스트 블록 (Java 13+)

여러 줄의 문자열을 편리하게 작성할 수 있습니다.

```java
package ch02.sec06;

public class TextBlockExample {
    public static void main(String[] args) {
        // 기존 방식
        String str1 = "" +
        "{\n" +
        "\t\"id\":\"winter\",\n" +
        "\t\"name\":\"눈송이\"\n"+
        "}";
        
        // 텍스트 블록 방식
        String str2 = """
        {
            "id":"winter",
            "name":"눈송이"
        }
        """;
        
        System.out.println(str1);
        System.out.println("------------------");
        System.out.println(str2);
        System.out.println("------------------");
        
        // 백슬래시로 줄바꿈 방지
        String str = """
        나는 자바를\
        학습합니다.
        나는 자바 고수가 될 겁니다.
        """;
        
        System.out.println(str);
    }
}
```

**실행 결과:**
```bash
{
	"id":"winter",
	"name":"눈송이"
}
------------------
{
	"id":"winter",
	"name":"눈송이"
}
------------------
나는 자바를학습합니다.
나는 자바 고수가 될 겁니다.
```

---

## 7. 자동 타입 변환 (Promotion)

### 자동 타입 변환이란?

작은 허용 범위의 타입이 큰 허용 범위의 타입으로 자동 변환되는 것

**타입 크기 순서:**
```
byte < short, char < int < long < float < double
```

> **주의:** long(8byte)보다 float(4byte)의 표현 범위가 더 큽니다!

```java
package ch02.sec07;

public class PromotionExample {
    public static void main(String[] args) {
        // 자동 타입 변환
        byte byteValue = 10;
        int intValue = byteValue;
        System.out.println("intValue: " + intValue);
        
        char charValue = '가';
        intValue = charValue;
        System.out.println("'가'의 유니코드: " + intValue);
        
        intValue = 50;
        long longValue = intValue;
        System.out.println("longValue: " + longValue);
        
        longValue = 100;
        float floatValue = longValue;
        System.out.println("floatValue: " + floatValue);
        
        floatValue = 100.5f;
        double doubleValue = floatValue;
        System.out.println("doubleValue: " + doubleValue);
    }
}
```

**실행 결과:**
```bash
intValue: 10
'가'의 유니코드: 44032
longValue: 50
floatValue: 100.0
doubleValue: 100.5
```

---

## 8. 강제 타입 변환 (Casting)

### 강제 타입 변환이란?

큰 허용 범위 타입을 작은 허용 범위 타입으로 변환하는 것

**문법:**
```java
작은타입 변수 = (작은타입) 큰타입변수;
```

```java
package ch02.sec08;

public class CastingExample {
    public static void main(String[] args) {
        int var1 = 10;
        byte var2 = (byte) var1;
        System.out.println(var2);
        
        long var3 = 300;
        int var4 = (int) var3;
        System.out.println(var4);
        
        int var5 = 65;
        char var6 = (char) var5;
        System.out.println(var6);
        
        double var7 = 3.14;
        int var8 = (int) var7;  // 소수점 이하 버림
        System.out.println(var8);
    }
}
```

**실행 결과:**
```bash
10
300
A
3
```

> **주의:** 강제 타입 변환 시 값 손실이 발생할 수 있습니다!

---

## 9. 연산식에서의 자동 타입 변환

### 연산 시 타입 변환 규칙

1. **정수 연산**: 결과는 기본적으로 `int` 타입
2. **실수 연산**: 결과는 기본적으로 `double` 타입
3. **서로 다른 타입의 연산**: 큰 타입으로 자동 변환

```java
package ch02.sec09;

public class OperationPromotionExample {
    public static void main(String[] args) {
        // 리터럴 연산은 컴파일 시점에 계산됨
        byte result1 = 10 + 20;
        System.out.println("result1: " + result1);
        
        // 변수 연산은 int 타입으로 변환
        byte v1 = 10;
        byte v2 = 20;
        int result2 = v1 + v2;
        System.out.println("result2: " + result2);

        // long이 포함되면 long으로 변환
        byte v3 = 10;
        int v4 = 100;
        long v5 = 1000L;
        long result3 = v3 + v4 + v5;
        System.out.println("result3: " + result3);
        
        // char 연산은 int로 변환
        char v6 = 'A';
        char v7 = 1;
        int result4 = v6 + v7;
        System.out.println("result4: " + result4);
        System.out.println("result4(char): " + (char)result4);
        
        // 정수 나눗셈은 몫만 반환
        int v8 = 10;
        int result5 = v8 / 4;
        System.out.println("result5: " + result5);
        
        // 실수가 포함되면 실수로 변환
        int v9 = 10;
        double result6 = v9 / 4.0;
        System.out.println("result6: " + result6);
        
        // 강제 타입 변환으로 실수 나눗셈
        int v10 = 1;
        int v11 = 2;
        double result7 = (double) v10 / v11;
        System.out.println("result7: " + result7);
    }
}
```

**실행 결과:**
```bash
result1: 30
result2: 30
result3: 1110
result4: 66
result4(char): B
result5: 2
result6: 2.5
result7: 0.5
```

### 문자열 연결 연산

```java
package ch02.sec09;

public class StringConcatExample {
    public static void main(String[] args) {
        // 좌에서 우로 순차 연산
        int result1 = 10 + 2 + 8;
        System.out.println("result1: " + result1);
        
        String result2 = 10 + 2 + "8";
        System.out.println("result2: " + result2);
        
        String result3 = 10 + "2" + 8;
        System.out.println("result3: " + result3);
        
        String result4 = "10" + 2 + 8;
        System.out.println("result4: " + result4);
        
        String result5 = "10" + (2 + 8);
        System.out.println("result5: " + result5);
    }
}
```

**실행 결과:**
```bash
result1: 20
result2: 128
result3: 1028
result4: 1028
result5: 1010
```

---

## 10. 문자열과 기본 타입 간 변환

### 문자열 → 기본 타입

```java
int value = Integer.parseInt("10");
double value = Double.parseDouble("3.14");
boolean value = Boolean.parseBoolean("true");
```

### 기본 타입 → 문자열

```java
String str = String.valueOf(10);
String str = String.valueOf(3.14);
String str = String.valueOf(true);
```

### 전체 예시

```java
package ch02.sec10;

public class PrimitiveAndStringConversionExample {
    public static void main(String[] args) {
        // 문자열 → 기본 타입
        int value1 = Integer.parseInt("10");
        double value2 = Double.parseDouble("3.14");
        boolean value3 = Boolean.parseBoolean("true");
        
        System.out.println("value1: " + value1);
        System.out.println("value2: " + value2);
        System.out.println("value3: " + value3);
        
        // 기본 타입 → 문자열
        String str1 = String.valueOf(10);
        String str2 = String.valueOf(3.14);
        String str3 = String.valueOf(true);
        
        System.out.println("str1: " + str1);
        System.out.println("str2: " + str2);
        System.out.println("str3: " + str3);
    }
}
```

**실행 결과:**
```bash
value1: 10
value2: 3.14
value3: true
str1: 10
str2: 3.14
str3: true
```

---

## 11. 변수 사용 범위 (Scope)

변수는 **선언된 블록 `{}` 내에서만** 사용할 수 있습니다.

```java
package ch02.sec11;

public class VariableScopeExample {
    public static void main(String[] args) {
        int v1 = 15;
        
        if(v1 > 10) {
            int v2 = v1 - 10;
            System.out.println("v2: " + v2);
        }
        
        // int v3 = v1 + v2 + 5;  // 컴파일 에러!
        // v2는 if 블록 밖에서 사용 불가
    }
}
```

---

## 12. 콘솔 출력

### printf() 메서드

형식화된 문자열을 출력할 때 사용합니다.

**형식 지정자:**

| 지정자 | 설명 | 예시 |
|--------|------|------|
| %d | 정수 | 123 |
| %f | 실수 | 3.14 |
| %s | 문자열 | "Hello" |
| %c | 문자 | 'A' |
| %b | 불린 | true |

**추가 옵션:**
- `%6d`: 6자리 확보 (오른쪽 정렬)
- `%-6d`: 6자리 확보 (왼쪽 정렬)
- `%06d`: 6자리 확보, 빈자리는 0으로 채움
- `%10.2f`: 전체 10자리, 소수점 이하 2자리

```java
package ch02.sec12;

public class PrintfExample {
    public static void main(String[] args) {
        int value = 123;
        System.out.printf("상품의 가격: %d원\n", value);
        System.out.printf("상품의 가격: %6d원\n", value);
        System.out.printf("상품의 가격: %-6d원\n", value);
        System.out.printf("상품의 가격: %06d원\n", value);
        
        double area = 3.14159 * 10 * 10;
        System.out.printf("반지름이 %d인 원의 넓이: %10.2f\n", 10, area);
        
        String name = "홍길동";
        String job = "도적";
        System.out.printf("%6d | %-10s | %10s\n", 1, name, job);
    }
}
```

**실행 결과:**
```bash
상품의 가격: 123원
상품의 가격:    123원
상품의 가격: 123   원
상품의 가격: 000123원
반지름이 10인 원의 넓이:     314.16
     1 | 홍길동        |         도적
```

---

## 13. 키보드 입력

### Scanner 클래스 사용

```java
package ch02.sec13;

import java.util.Scanner;

public class ScannerExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("x 값 입력: ");
        String strX = scanner.nextLine();
        int x = Integer.parseInt(strX);
        
        System.out.print("y 값 입력: ");
        String strY = scanner.nextLine();
        int y = Integer.parseInt(strY);
        
        int result = x + y;
        System.out.println("x + y: " + result);
        System.out.println();
        
        while(true) {
            System.out.print("입력 문자열: ");
            String data = scanner.nextLine();
            if(data.equals("q")) {
                break;
            }
            System.out.println("출력 문자열: " + data);
            System.out.println();
        }
        
        System.out.println("종료");
        scanner.close();  // Scanner 자원 해제
    }
}
```

**실행 결과:**
```bash
x 값 입력: 3
y 값 입력: 5
x + y: 8

입력 문자열: Hello
출력 문자열: Hello

입력 문자열: 안녕
출력 문자열: 안녕

입력 문자열: q
종료
```

---

## 📌 정리

### 기본 타입 정리표

| 분류 | 타입 | 크기 | 기본값 |
|------|------|------|--------|
| **정수** | byte | 1byte | 0 |
| | short | 2byte | 0 |
| | **int** | 4byte | 0 |
| | long | 8byte | 0L |
| **실수** | float | 4byte | 0.0f |
| | **double** | 8byte | 0.0 |
| **문자** | char | 2byte | '\u0000' |
| **논리** | boolean | 1byte | false |

### 타입 변환 정리

1. **자동 타입 변환**: 작은 타입 → 큰 타입
2. **강제 타입 변환**: 큰 타입 → 작은 타입 (데이터 손실 가능)
3. **연산 시 변환**: 기본적으로 int/double로 변환

### 주의사항

- long 리터럴은 `L` 접미사 필수
- float 리터럴은 `f` 접미사 필수
- 정수 나눗셈은 소수점 버림
- 변수는 선언된 블록 내에서만 유효

---