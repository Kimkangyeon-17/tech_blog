# Java 기초 - 연산자(Operators)

## 1. 부호 연산자

부호 연산자는 변수나 리터럴의 부호를 변경합니다.

```java
package ch03.sec01;

public class SignOperatorExample {
    public static void main(String[] args) {
        int x = -100;
        x = -x;  // 부호 반전
        System.out.println("x: " + x);
        
        byte b = 100;
        int y = -b;  // byte가 int로 자동 변환 후 부호 반전
        System.out.println("y: " + y);
    }
}
```

**실행 결과:**
```bash
x: 100
y: -100
```

> **주의:** 부호 연산자를 사용하면 byte, short 타입은 int 타입으로 자동 변환됩니다.

---

## 2. 증감 연산자

증감 연산자(`++`, `--`)는 변수의 값을 1 증가시키거나 1 감소시킵니다.

### 증감 연산자의 위치에 따른 차이

```java
package ch03.sec01;

public class IncreaseDecreaseOperatorExample {
    public static void main(String[] args) {
        int x = 10;
        int y = 10;
        int z;
        
        x++;  // 후위 증가: x = x + 1
        ++x;  // 전위 증가: x = x + 1
        System.out.println("x: " + x);  // 12
        
        System.out.println("-------------");
        
        y--;  // 후위 감소: y = y - 1
        --y;  // 전위 감소: y = y - 1
        System.out.println("y: " + y);  // 8
        
        System.out.println("-------------");
        
        z = x++;  // z에 x값 대입 후 x 증가
        System.out.println("z: " + z);  // 12
        System.out.println("x: " + x);  // 13
        
        System.out.println("-------------");
        
        z = ++x;  // x 증가 후 z에 대입
        System.out.println("z: " + z);  // 14
        System.out.println("x: " + x);  // 14
        
        System.out.println("-------------");
        
        z = ++x + y++;  // x 먼저 증가, y는 연산 후 증가
        System.out.println("z: " + z);  // 15 + 8 = 23
        System.out.println("x: " + x);  // 15
        System.out.println("y: " + y);  // 9
    }
}
```

**실행 결과:**
```bash
x: 12
-------------
y: 8
-------------
z: 12
x: 13
-------------
z: 14
x: 14
-------------
z: 23
x: 15
y: 9
```

### 증감 연산자 정리

| 연산자 | 위치 | 설명 | 예시 |
|--------|------|------|------|
| `++` | 전위 | 값을 먼저 증가시킨 후 사용 | `y = ++x;` |
| `++` | 후위 | 값을 사용한 후 증가 | `y = x++;` |
| `--` | 전위 | 값을 먼저 감소시킨 후 사용 | `y = --x;` |
| `--` | 후위 | 값을 사용한 후 감소 | `y = x--;` |

---

## 3. 산술 연산자

산술 연산자는 사칙연산과 나머지 연산을 수행합니다.

### 산술 연산의 타입 변환 규칙

1. **피연산자가 정수 타입**(byte, short, char, int)이면 → 결과는 **int 타입**
2. **피연산자 중 하나라도 long 타입**이면 → 결과는 **long 타입**
3. **피연산자 중 하나라도 실수 타입**이면 → 결과는 **실수 타입**

```java
package ch03.sec02;

public class ArithmeticOperatorExample {
    public static void main(String[] args) {
        byte v1 = 10;
        byte v2 = 4;
        int v3 = 5;
        long v4 = 10L;
        
        // 모든 피연산자가 int 타입으로 자동 변환 후 연산
        int result1 = v1 + v2;
        System.out.println("result1: " + result1);
        
        // long이 포함되어 모든 피연산자가 long으로 변환
        long result2 = v1 + v2 - v4;
        System.out.println("result2: " + result2);
        
        // double로 강제 변환 후 연산
        double result3 = (double)v1 / v2;
        System.out.println("result3: " + result3);

        // 나머지 연산
        int result4 = v1 % v2;
        System.out.println("result4: " + result4);
    }
}
```

**실행 결과:**
```bash
result1: 14
result2: 4
result3: 2.5
result4: 2
```

---

## 4. 오버플로우와 언더플로우

### 오버플로우(Overflow)
타입이 허용하는 **최대값을 초과**하면 최소값으로 순환합니다.

### 언더플로우(Underflow)
타입이 허용하는 **최소값을 초과**하면 최대값으로 순환합니다.

```java
package ch03.sec03;

public class OverflowUnderflowExample {
    public static void main(String[] args) {
        // 오버플로우 예시
        byte var1 = 125;
        for(int i = 0; i < 5; i++) {
            var1++;
            System.out.println("var1: " + var1);
        }
        
        System.out.println("---------------");
        
        // 언더플로우 예시
        byte var2 = -125;
        for(int i = 0; i < 5; i++) {
            var2--;
            System.out.println("var2: " + var2);
        }
    }
}
```

**실행 결과:**
```bash
var1: 126
var1: 127
var1: -128  ← 오버플로우 발생!
var1: -127
var1: -126
---------------
var2: -126
var2: -127
var2: -128
var2: 127   ← 언더플로우 발생!
var2: 126
```

> **중요:** 오버플로우/언더플로우는 예외를 발생시키지 않으므로 주의해야 합니다!

---

## 5. 정확한 계산은 정수 연산으로

### 부동소수점 연산의 정밀도 문제

```java
package ch03.sec04;

public class AccuracyExample {
    public static void main(String[] args) {
        int apple = 1;
        double pieceUnit = 0.1;
        int number = 7;
        
        double result = apple - number * pieceUnit;
        System.out.println("사과 1개에서 남은 양: " + result);
    }
}
```

**실행 결과:**
```bash
사과 1개에서 남은 양: 0.29999999999999993
```

결과가 정확히 0.3이 아닙니다! 이는 **부동소수점 방식**의 정밀도 한계 때문입니다.

### 해결 방법: 정수 연산 사용

```java
package ch03.sec04;

public class AccuracyExample2 {
    public static void main(String[] args) {
        int apple = 1;
        int totalPieces = apple * 10;  // 10조각으로 변환
        int number = 7;
        
        int result = totalPieces - number;
        System.out.println("10조각에서 남은 조각: " + result);
        System.out.println("사과 1개에서 남은 양: " + result / 10.0);
    }
}
```

**실행 결과:**
```bash
10조각에서 남은 조각: 3
사과 1개에서 남은 양: 0.3
```

> **정밀도가 중요한 금융 계산 등에서는 정수 연산을 사용하거나 `BigDecimal` 클래스를 활용해야 합니다.**

---

## 6. NaN과 Infinity 처리

### 나눗셈 연산의 주의사항

```java
int x = 5;
int y = 0;
int result = x / y;  // ArithmeticException 발생!
```

정수를 0으로 나누면 **예외 발생**합니다.

```java
double x = 5.0;
double y = 0.0;
double result = x / y;  // Infinity
```

실수를 0.0으로 나누면 **Infinity** 또는 **NaN**이 됩니다.

### Infinity와 NaN 확인

```java
package ch03.sec05;

public class InfinityAndNaNExample {
    public static void main(String[] args) {
        int x = 5;
        double y = 0.0;
        double z = x / y;
        
        System.out.println(z + 2);
        
        // Infinity 또는 NaN 확인 후 처리
        if(Double.isInfinite(z) || Double.isNaN(z)) {
            System.out.println("값 산출 불가");
        } else {
            System.out.println(z + 2);
        }
    }
}
```

**실행 결과:**
```bash
Infinity
값 산출 불가
```

### NaN 발생 경우

```java
double result1 = 5 / 0.0;    // Infinity
double result2 = 5 % 0.0;    // NaN
double result3 = 0.0 / 0.0;  // NaN
```

> **중요:** Infinity나 NaN 상태에서 연산을 계속하면 결과가 모두 Infinity/NaN이 되므로, 반드시 확인 후 처리해야 합니다!

---

## 7. 비교 연산자

비교 연산자는 두 피연산자를 비교하여 **boolean 타입**(`true` 또는 `false`)을 반환합니다.

### 비교 연산자 종류

| 연산자 | 의미 | 예시 | 결과 |
|--------|------|------|------|
| `==` | 같다 | `5 == 5` | `true` |
| `!=` | 다르다 | `5 != 3` | `true` |
| `>` | 크다 | `5 > 3` | `true` |
| `>=` | 크거나 같다 | `5 >= 5` | `true` |
| `<` | 작다 | `5 < 3` | `false` |
| `<=` | 작거나 같다 | `5 <= 5` | `true` |

```java
package ch03.sec06;

public class CompareOperatorExample {
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 10;
        
        boolean result1 = (num1 == num2);
        boolean result2 = (num1 != num2);
        boolean result3 = (num1 <= num2);
        System.out.println("result1: " + result1);
        System.out.println("result2: " + result2);
        System.out.println("result3: " + result3);
        
        // char 타입 비교 (유니코드 값 비교)
        char char1 = 'A';  // 65
        char char2 = 'B';  // 66
        boolean result4 = (char1 < char2);
        System.out.println("result4: " + result4);
        
        // 정수와 실수 비교
        int num3 = 1;
        double num4 = 1.0;
        boolean result5 = (num3 == num4);
        System.out.println("result5: " + result5);
        
        // 실수 비교의 주의점
        float num5 = 0.1f;
        double num6 = 0.1;
        boolean result6 = (num5 == num6);  // 정밀도 차이로 false
        boolean result7 = (num5 == (float)num6);
        System.out.println("result6: " + result6);
        System.out.println("result7: " + result7);
        
        // 문자열 비교
        String str1 = "자바";
        String str2 = "Java";
        boolean result8 = (str1.equals(str2));
        boolean result9 = (!str1.equals(str2));
        System.out.println("result8: " + result8);
        System.out.println("result9: " + result9);
    }
}
```

**실행 결과:**
```bash
result1: true
result2: false
result3: true
result4: true
result5: true
result6: false
result7: true
result8: false
result9: true
```

> **문자열 비교는 `==`가 아닌 `equals()` 메서드를 사용해야 합니다!**

---

## 8. 논리 연산자

논리 연산자는 boolean 타입만을 피연산자로 받아 boolean 값을 반환합니다.

### 논리 연산자 종류

| 연산자 | 의미 | 설명 |
|--------|------|------|
| `&&` | AND (논리곱) | 양쪽 모두 true일 때 true |
| `||` | OR (논리합) | 한쪽이라도 true면 true |
| `^` | XOR (배타적 논리합) | 양쪽이 다를 때 true |
| `!` | NOT (논리 부정) | true ↔ false 반전 |

### && vs & (|| vs |)

- **`&&` (단락 평가)**: 앞의 조건이 false면 뒤의 조건을 평가하지 않음
- **`&` (완전 평가)**: 앞의 조건과 관계없이 모든 조건을 평가

```java
package ch03.sec07;

public class LogicalOperatorExample {
    public static void main(String[] args) {
        int charCode = 'A';  // 65
        
        // & 연산자: 양쪽 모두 평가
        if ((65 <= charCode) & (charCode <= 90)) {
            System.out.println("대문자입니다.");
        }
        
        // && 연산자: 단락 평가 (더 효율적)
        if ((97 <= charCode) && (charCode <= 122)) {
            System.out.println("소문자입니다.");
        }
        
        if ((48 <= charCode) && (charCode <= 57)) {
            System.out.println("0~9 숫자입니다.");
        }
        
        System.out.println("-------------");
        
        int value = 6;
        
        // | 연산자: 양쪽 모두 평가
        if ((value % 2 == 0) | (value % 3 == 0)) {
            System.out.println("2 또는 3의 배수입니다.");
        }
        
        // || 연산자: 단락 평가
        boolean result = (value % 2 == 0) || (value % 3 == 0);
        if (!result) {
            System.out.println("2 또는 3의 배수가 아닙니다.");
        }
    }
}
```

**실행 결과:**
```bash
대문자입니다.
-------------
2 또는 3의 배수입니다.
```

### 논리 연산 진리표

| A | B | A && B | A \|\| B | A ^ B | !A |
|---|---|--------|----------|-------|-----|
| true | true | true | true | false | false |
| true | false | false | true | true | false |
| false | true | false | true | true | true |
| false | false | false | false | false | true |

> **권장:** 대부분의 경우 단락 평가를 수행하는 `&&`, `||`를 사용하는 것이 효율적입니다.

---

## 9. 비트 논리 연산자

비트 논리 연산자는 **비트(bit) 단위**로 논리 연산을 수행합니다.

### 비트 연산자 종류

| 연산자 | 의미 | 설명 |
|--------|------|------|
| `&` | AND | 두 비트 모두 1일 때 1 |
| `|` | OR | 두 비트 중 하나라도 1이면 1 |
| `^` | XOR | 두 비트가 다르면 1 |
| `~` | NOT | 비트 반전 (0 ↔ 1) |

### 비트 연산 예시

```
  45 = 00101101
& 25 = 00011001
-----------------
   9 = 00001001
```

```java
package ch03.sec08;

public class BitLogicExample {
    public static void main(String[] args) {
        System.out.println("45 & 25 = " + (45 & 25));  // 9
        System.out.println("45 | 25 = " + (45 | 25));  // 61
        System.out.println("45 ^ 25 = " + (45 ^ 25));  // 52
        System.out.println("~45 = " + (~45));          // -46
        System.out.println("-----------------");
        
        // byte를 unsigned로 변환
        byte receiveData = -120;
        
        // 방법 1: 255와 AND 연산
        int unsignedInt1 = receiveData & 255;
        System.out.println(unsignedInt1);
        
        // 방법 2: Byte.toUnsignedInt() 메서드
        int unsignedInt2 = Byte.toUnsignedInt(receiveData);
        System.out.println(unsignedInt2);
        
        // 확인: int를 byte로 변환
        int test = 136;
        byte btest = (byte)test;
        System.out.println(btest);
    }
}
```

**실행 결과:**
```bash
45 & 25 = 9
45 | 25 = 61
45 ^ 25 = 52
~45 = -46
-----------------
136
136
-120
```

### 비트 연산 활용
- 플래그(flag) 관리
- 권한 설정
- 네트워크 프로토콜
- 암호화 알고리즘

---

## 10. 비트 이동 연산자

비트 이동 연산자는 비트를 좌측 또는 우측으로 이동시킵니다.

### 비트 이동 연산자 종류

| 연산자 | 의미 | 설명 |
|--------|------|------|
| `<<` | 왼쪽 시프트 | 비트를 왼쪽으로 이동, 오른쪽 빈자리는 0 |
| `>>` | 오른쪽 시프트 | 비트를 오른쪽으로 이동, 왼쪽 빈자리는 부호 비트로 채움 |
| `>>>` | 부호 없는 오른쪽 시프트 | 왼쪽 빈자리를 0으로 채움 |

### 비트 이동 연산의 수학적 의미

- `a << n` = a × 2ⁿ
- `a >> n` = a ÷ 2ⁿ (음수 유지)

```java
package ch03.sec09;

public class BitShiftExample {
    public static void main(String[] args) {
        int num1 = 1;
        int result1 = num1 << 3;  // 1 * 2^3
        int result2 = num1 * (int)Math.pow(2, 3);
        System.out.println("result1: " + result1);
        System.out.println("result2: " + result2);
        
        int num2 = -8;
        int result3 = num2 >> 3;  // -8 / 2^3
        int result4 = num2 / (int)Math.pow(2, 3);
        System.out.println("result3: " + result3);
        System.out.println("result4: " + result4);
    }
}
```

**실행 결과:**
```bash
result1: 8
result2: 8
result3: -1
result4: -1
```

### 비트 이동을 이용한 바이트 추출

```java
package ch03.sec09;

public class BitShiftExample2 {
    public static void main(String[] args) {
        int value = 772;  // 00000000 00000000 00000011 00000100
        
        // 첫 번째 바이트 (최상위)
        byte byte1 = (byte)(value >>> 24);
        int int1 = byte1 & 255;
        System.out.println("첫 번째 바이트 부호 없는 값: " + int1);
        
        // 두 번째 바이트
        byte byte2 = (byte)(value >>> 16);
        int int2 = Byte.toUnsignedInt(byte2);
        System.out.println("두 번째 바이트 부호 없는 값: " + int2);
        
        // 세 번째 바이트
        byte byte3 = (byte)(value >>> 8);
        int int3 = byte3 & 255;
        System.out.println("세 번째 바이트 부호 없는 값: " + int3);
        
        // 네 번째 바이트 (최하위)
        byte byte4 = (byte)value;
        int int4 = Byte.toUnsignedInt(byte4);
        System.out.println("네 번째 바이트 부호 없는 값: " + int4);
    }
}
```

**실행 결과:**
```bash
첫 번째 바이트 부호 없는 값: 0
두 번째 바이트 부호 없는 값: 0
세 번째 바이트 부호 없는 값: 3
네 번째 바이트 부호 없는 값: 4
```

---

## 11. 대입 연산자

대입 연산자는 우측 피연산자의 값을 좌측 피연산자(변수)에 저장합니다.

### 복합 대입 연산자

| 연산자 | 의미 | 동일한 표현 |
|--------|------|------------|
| `+=` | 더한 후 대입 | `a = a + b` |
| `-=` | 뺀 후 대입 | `a = a - b` |
| `*=` | 곱한 후 대입 | `a = a * b` |
| `/=` | 나눈 후 대입 | `a = a / b` |
| `%=` | 나머지 후 대입 | `a = a % b` |
| `&=` | AND 후 대입 | `a = a & b` |
| `|=` | OR 후 대입 | `a = a | b` |
| `^=` | XOR 후 대입 | `a = a ^ b` |
| `<<=` | 왼쪽 시프트 후 대입 | `a = a << b` |
| `>>=` | 오른쪽 시프트 후 대입 | `a = a >> b` |
| `>>>=` | 부호 없는 오른쪽 시프트 후 대입 | `a = a >>> b` |

```java
package ch03.sec10;

public class AssignmentOperatorExample {
    public static void main(String[] args) {
        int result = 0;
        
        result += 10;  // result = result + 10
        System.out.println("result=" + result);
        
        result -= 5;   // result = result - 5
        System.out.println("result=" + result);
        
        result *= 3;   // result = result * 3
        System.out.println("result=" + result);
        
        result /= 5;   // result = result / 5
        System.out.println("result=" + result);
        
        result %= 3;   // result = result % 3
        System.out.println("result=" + result);
    }
}
```

**실행 결과:**
```bash
result=10
result=5
result=15
result=3
result=0
```

---

## 12. 삼항(조건) 연산자

삼항 연산자는 **조건식의 결과에 따라** 값을 선택합니다.

### 문법
```java
조건식 ? 값1 : 값2
```

- 조건식이 `true`면 → 값1 선택
- 조건식이 `false`면 → 값2 선택

```java
package ch03.sec11;

public class ConditionalOperationExample {
    public static void main(String[] args) {
        int score = 85;
        char grade = (score > 90) ? 'A' : ((score > 80) ? 'B' : 'C');
        System.out.println(score + "점은 " + grade + "등급입니다.");
    }
}
```

**실행 결과:**
```bash
85점은 B등급입니다.
```

### 삼항 연산자 중첩 예시

```java
int age = 25;
String type = (age < 20) ? "미성년자" : 
              (age < 65) ? "성인" : "노인";
```

> **가독성:** 삼항 연산자는 간단한 조건에만 사용하고, 복잡한 조건은 if-else 문을 사용하는 것이 좋습니다.

---

## 13. 연산자 우선순위와 방향

### 연산자 우선순위 (높은 순서대로)

| 순위 | 연산자 | 연산 방향 |
|------|--------|-----------|
| 1 | 증감(`++`, `--`), 부호(`+`, `-`), 비트(`~`), 논리(`!`) | ← |
| 2 | 산술(`*`, `/`, `%`) | → |
| 3 | 산술(`+`, `-`) | → |
| 4 | 시프트(`<<`, `>>`, `>>>`) | → |
| 5 | 비교(`<`, `>`, `<=`, `>=`, `instanceof`) | → |
| 6 | 비교(`==`, `!=`) | → |
| 7 | 비트 AND(`&`) | → |
| 8 | 비트 XOR(`^`) | → |
| 9 | 비트 OR(`|`) | → |
| 10 | 논리 AND(`&&`) | → |
| 11 | 논리 OR(`||`) | → |
| 12 | 삼항(`?:`) | → |
| 13 | 대입(`=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `|=`, `<<=`, `>>=`, `>>>=`) | ← |

### 우선순위 예시

```java
int result = 10 + 20 * 3;  // 70 (곱셈 먼저)

int x = 10;
boolean b = x > 5 && x < 20;  // true (비교 먼저, 그다음 논리)

int y = 3 + 2 << 1;  // 10 ((3+2) << 1 = 5 << 1)
```

> **권장:** 우선순위가 헷갈리면 **괄호 `()`를 사용**하여 명확하게 표현하세요!

---

## 📌 정리

### 연산자 분류

| 분류 | 연산자 |
|------|--------|
| **단항** | `+`, `-`, `++`, `--`, `!`, `~` |
| **이항** | 산술(`+`, `-`, `*`, `/`, `%`), 비교(`<`, `>`, `==`, `!=`), 논리(`&&`, `||`, `&`, `|`, `^`), 비트 이동(`<<`, `>>`, `>>>`) |
| **삼항** | `? :` |
| **대입** | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, 등 |

### 주요 주의사항

1. **정수 연산**: 기본적으로 int 타입으로 변환
2. **실수 연산**: 정밀도 문제로 금융 계산 시 주의
3. **나눗셈**: 0으로 나누기, NaN/Infinity 체크 필수
4. **비트 연산**: 부호 있는 타입에서 시프트 연산 주의
5. **단락 평가**: `&&`, `||` 사용으로 효율성 향상
6. **문자열 비교**: `equals()` 메서드 사용
7. **오버플로우**: 예외 발생 없이 값이 순환하므로 주의

### 디버깅 팁

- 복잡한 연산식은 중간 결과를 출력하여 확인
- 괄호를 사용하여 연산 순서를 명확히 표현
- 형 변환이 자동으로 일어나는 경우를 주의

---

> 다음 학습 주제: **조건문(if, switch)**과 **반복문(for, while)** 🚀