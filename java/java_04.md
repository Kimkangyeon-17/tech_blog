# Java 참조 타입(Reference Type)

## 1. 데이터 타입 분류

### Java의 두 가지 데이터 타입

Java의 데이터 타입은 크게 **기본 타입(Primitive Type)**과 **참조 타입(Reference Type)**으로 분류됩니다.

```
데이터 타입
├── 기본 타입 (Primitive Type)
│   ├── 정수: byte, short, int, long
│   ├── 실수: float, double
│   ├── 문자: char
│   └── 논리: boolean
└── 참조 타입 (Reference Type)
    ├── 배열 (Array)
    ├── 열거 (Enum)
    ├── 클래스 (Class)
    └── 인터페이스 (Interface)
```

### 기본 타입 vs 참조 타입

| 구분 | 기본 타입 | 참조 타입 |
| --- | --- | --- |
| **저장 내용** | 실제 **값** 저장 | 객체의 **주소(번지)** 저장 |
| **메모리 위치** | 스택(Stack) 영역 | 힙(Heap) 영역 |
| **기본값** | 타입별 0 또는 false | null |
| **비교 연산** | 값 비교 | 주소 비교 |

```
// 기본 타입
int age = 25;           // 스택에 25 저장

// 참조 타입
String name = "홍길동";  // 스택에는 주소, 힙에 "홍길동" 객체 저장
```

---

## 2\. JVM 메모리 구조

### Java 메모리 영역

JVM(Java Virtual Machine)은 프로그램 실행 시 **메모리를 세 가지 영역**으로 나누어 관리합니다.

```
┌─────────────────────────────────────┐
│        메소드 영역 (Method Area)      │
│   - 클래스 정보, 상수, static 변수    │
│   - 메소드 코드, 생성자 코드          │
└─────────────────────────────────────┘
┌─────────────────────────────────────┐
│          힙 영역 (Heap Area)          │
│   - 객체와 배열이 생성되는 영역       │
│   - 가비지 컬렉션 대상               │
└─────────────────────────────────────┘
┌─────────────────────────────────────┐
│        스택 영역 (Stack Area)         │
│   - 메소드 호출 시 생성되는 프레임    │
│   - 지역 변수, 매개변수 저장          │
└─────────────────────────────────────┘
```

### 1) 메소드 영역 (Method Area)

-   **클래스 로더**가 읽은 **바이트코드(.class 파일)** 저장
-   클래스별로 다음 정보 저장:
    -   상수 (final 변수)
    -   정적 필드 (static 변수)
    -   메소드 코드
    -   생성자 코드
-   **모든 스레드가 공유**
-   JVM 시작 시 생성, 종료 시 소멸

### 2) 힙 영역 (Heap Area)

-   **객체와 배열**이 생성되는 영역
-   **가비지 컬렉션(Garbage Collection)** 대상
-   **모든 스레드가 공유**
-   JVM 시작 시 생성, 종료 시 소멸

**예시:**

```
String str = new String("Hello");
// str 변수: 스택 영역
// "Hello" 객체: 힙 영역
// str은 힙 영역의 객체 주소를 저장
```

### 3) 스택 영역 (Stack Area)

-   **메소드 호출**마다 프레임(Frame) 생성
-   **지역 변수**와 **매개변수** 저장
-   메소드 실행 완료 시 프레임 **자동 제거**
-   **스레드마다 독립적**으로 생성

**예시:**

```
public void method1() {
    int x = 10;      // 스택에 x 생성
    method2(x);      // 새로운 프레임 생성
}                    // method1 프레임 제거

public void method2(int y) {
    int z = 20;      // 스택에 y, z 생성
}                    // method2 프레임 제거
```

### 메모리 사용 예시

```
public class MemoryExample {
    static int staticVar = 100;  // 메소드 영역

    public static void main(String[] args) {
        int localVar = 10;           // 스택 영역
        String str = new String("Java");  // str: 스택, "Java": 힙
        int[] arr = new int[3];      // arr: 스택, 배열 객체: 힙
    }
}
```

---

## 3\. 참조 변수의 ==, != 연산

### 주소 비교 vs 값 비교

참조 타입 변수에 `==`, `!=` 연산자를 사용하면 **객체의 주소를 비교**합니다.

```
package ch05.sec03;

public class ReferenceVariableCompareExample {
    public static void main(String[] args) {
        int[] arr1;
        int[] arr2;
        int[] arr3;

        arr1 = new int[] {1, 2, 3};  // 힙에 첫 번째 배열 생성
        arr2 = new int[] {1, 2, 3};  // 힙에 두 번째 배열 생성
        arr3 = arr2;                 // arr2의 주소를 복사

        System.out.println(arr1 == arr2);  // false (서로 다른 객체)
        System.out.println(arr2 == arr3);  // true (같은 객체 참조)
    }
}
```

**실행 결과:**

```
false
true
```

### 메모리 상태

```
Stack                    Heap
┌──────────┐            ┌──────────────┐
│ arr1 ──→ │            │ [1, 2, 3]    │ ← arr1
└──────────┘            └──────────────┘
┌──────────┐            ┌──────────────┐
│ arr2 ──→ │────┐       │ [1, 2, 3]    │ ← arr2, arr3
└──────────┘    │       └──────────────┘
┌──────────┐    │
│ arr3 ──→ │────┘
└──────────┘
```

> **중요:** `==` 연산자는 **주소를 비교**하므로, 내용이 같아도 다른 객체면 `false`입니다!

---

## 4\. null과 NullPointerException

### null이란?

`null`은 참조 타입 변수가 **아무 객체도 참조하지 않는 상태**를 나타냅니다.

```
String str = null;  // str은 어떤 String 객체도 참조하지 않음
```

### NullPointerException

null 상태의 참조 변수로 **객체에 접근하려고 하면** 발생하는 예외입니다.

```
package ch05.sec04;

public class NullPointerExceptionExample {
    public static void main(String[] args) {
        int[] intArray = null;
        // intArray[0] = 10;  // NullPointerException 발생!

        String str = null;
        // System.out.println("총 문자 수: " + str.length());  // NullPointerException 발생!
    }
}
```

**에러 메시지:**

```
Exception in thread "main" java.lang.NullPointerException: 
Cannot store to int array because "intArray" is null

Exception in thread "main" java.lang.NullPointerException: 
Cannot invoke "String.length()" because "str" is null
```

### null 체크 방법

```
if (str != null) {
    System.out.println(str.length());  // 안전
}
```

---

## 5\. 가비지 컬렉션 (Garbage Collection)

### 가비지(Garbage)란?

더 이상 참조되지 않는 객체를 **가비지(쓰레기)**라고 합니다.

```
package ch05.sec04;

public class GarbageObjectExample {
    public static void main(String[] args) {
        String hobby = "여행";
        hobby = null;  // "여행" 객체는 이제 가비지

        String kind1 = "자동차";
        String kind2 = kind1;
        kind1 = null;  // kind2가 여전히 참조하므로 가비지 아님
        System.out.println("kind2: " + kind2);
    }
}
```

**실행 결과:**

```
kind2: 자동차
```

### 메모리 상태 변화

```
초기 상태:
hobby → "여행"

hobby = null 후:
hobby → X    "여행" ← 가비지!

kind1 → "자동차" ← kind2
kind1 = null 후:
kind1 → X    "자동차" ← kind2 (여전히 참조됨)
```

### 가비지 컬렉터

-   JVM의 **가비지 컬렉터(Garbage Collector)**가 자동으로 가비지 제거
-   개발자가 직접 메모리 해제할 필요 없음
-   메모리 효율적 관리

---

## 6\. String 타입

### String의 특수성

String은 **참조 타입**이지만 특별한 특징이 있습니다.

#### 1) String Pool (문자열 상수 풀)

리터럴로 생성된 문자열은 **String Pool**에 저장되어 **재사용**됩니다.

```
package ch05.sec05;

public class EqualsExample {
    public static void main(String[] args) {
        String strVar1 = "홍길동";  // String Pool에 생성
        String strVar2 = "홍길동";  // String Pool의 기존 객체 재사용

        if (strVar1 == strVar2) {
            System.out.println("strVar1과 strVar2는 참조가 같음");
        } else {
            System.out.println("strVar1과 strVar2는 참조가 다름");
        }

        if (strVar1.equals(strVar2)) {
            System.out.println("strVar1과 strVar2는 문자열이 같음");
        }

        String strVar3 = new String("홍길동");  // 힙에 새 객체 생성
        String strVar4 = new String("홍길동");  // 힙에 또 다른 새 객체 생성

        if (strVar3 == strVar4) {
            System.out.println("strVar3과 strVar4는 참조가 같음");
        } else {
            System.out.println("strVar3과 strVar4는 참조가 다름");
        }

        if (strVar3.equals(strVar4)) {
            System.out.println("strVar3과 strVar4는 문자열이 같음");
        }
    }
}
```

**실행 결과:**

```
strVar1과 strVar2는 참조가 같음
strVar1과 strVar2는 문자열이 같음
strVar3과 strVar4는 참조가 다름
strVar3과 strVar4는 문자열이 같음
```

### String Pool 메모리 구조

```
String Pool (Heap 영역)
┌─────────────┐
│  "홍길동"    │ ← strVar1, strVar2 (재사용)
└─────────────┘

Heap 영역
┌─────────────┐
│  "홍길동"    │ ← strVar3
└─────────────┘
┌─────────────┐
│  "홍길동"    │ ← strVar4
└─────────────┘
```

> **핵심:**
> 
> -   `==`: **주소(참조)** 비교
> -   `equals()`: **내용(값)** 비교

### 빈 문자열

```
package ch05.sec05;

public class EmptyStringExample {
    public static void main(String[] args) {
        String hobby = "";  // 빈 문자열 (null이 아님!)

        if (hobby.equals("")) {
            System.out.println("hobby 변수가 참조하는 String 객체는 빈 문자열");
        }
    }
}
```

**실행 결과:**

```
hobby 변수가 참조하는 String 객체는 빈 문자열
```

> **주의:** `""` (빈 문자열)과 `null`은 다릅니다!
> 
> -   `""`: String 객체가 존재하지만 내용이 없음
> -   `null`: 아무 객체도 참조하지 않음

---

## 7\. String 주요 메소드

### 1) charAt() - 문자 추출

특정 **인덱스의 문자**를 가져옵니다.

```
package ch05.sec05;

public class CharAtExample {
    public static void main(String[] args) {
        String ssn = "9506241230123";
        char sex = ssn.charAt(6);  // 인덱스 6의 문자

        switch (sex) {
            case '1':
            case '3':
                System.out.println("남자입니다.");
                break;
            case '2':
            case '4':
                System.out.println("여자입니다.");
                break;
        }
    }
}
```

**실행 결과:**

```
남자입니다.
```

> **인덱스:** 문자열은 0부터 시작합니다. (0, 1, 2, 3, ...)

---

### 2) length() - 문자열 길이

문자열의 **문자 개수**를 반환합니다.

```
package ch05.sec05;

public class LengthExample {
    public static void main(String[] args) {
        String ssn = "9506241230123";
        int length = ssn.length();

        if (length == 13) {
            System.out.println("주민등록번호 자릿수가 맞습니다.");
        } else {
            System.out.println("주민등록번호 자릿수가 아닙니다.");
        }
    }
}
```

**실행 결과:**

```
주민등록번호 자릿수가 맞습니다.
```

---

### 3) replace() - 문자열 대체

특정 문자열을 **다른 문자열로 치환**합니다.

```
package ch05.sec05;

public class ReplaceExample {
    public static void main(String[] args) {
        String oldStr = "자바 문자열은 불변입니다. 자바 문자열은 String입니다.";
        String newStr = oldStr.replace("자바", "JAVA");

        System.out.println(oldStr);
        System.out.println(newStr);
    }
}
```

**실행 결과:**

```
자바 문자열은 불변입니다. 자바 문자열은 String입니다.
JAVA 문자열은 불변입니다. JAVA 문자열은 String입니다.
```

> **String의 불변성:** 원본 문자열은 변경되지 않고, **새로운 문자열이 생성**됩니다.

---

### 4) substring() - 문자열 자르기

문자열의 **일부를 추출**합니다.

```
package ch05.sec05;

public class SubstringExample {
    public static void main(String[] args) {
        String ssn = "990101-1234567";

        // substring(시작인덱스, 끝인덱스): 시작 ~ 끝-1까지
        String firstNum = ssn.substring(0, 6);
        System.out.println(firstNum);

        // substring(시작인덱스): 시작 ~ 끝까지
        String secondNum = ssn.substring(7);
        System.out.println(secondNum);
    }
}
```

**실행 결과:**

```
990101
1234567
```

**인덱스 범위:**

```
"990101-1234567"
 0123456789...

substring(0, 6): 인덱스 0~5
substring(7):    인덱스 7~끝
```

---

### 5) indexOf() / contains() - 문자열 찾기

특정 문자열이 **포함되어 있는지 확인**하고 **위치를 찾습니다**.

```
package ch05.sec05;

public class IndexOfContainsExample {
    public static void main(String[] args) {
        String subject = "자바 프로그래밍";

        // indexOf(): 문자열의 시작 인덱스 반환 (없으면 -1)
        int location = subject.indexOf("프로그래밍");
        System.out.println(location);

        String substring = subject.substring(location);
        System.out.println(substring);

        location = subject.indexOf("자바");
        if (location != -1) {
            System.out.println("자바와 관련된 책이군요");
        } else {
            System.out.println("자바와 관련 없는 책이군요");
        }

        // contains(): 포함 여부를 boolean으로 반환
        boolean result = subject.contains("자바");
        if (result) {
            System.out.println("자바와 관련된 책이군요");
        } else {
            System.out.println("자바와 관련 없는 책이군요");
        }
    }
}
```

**실행 결과:**

```
3
프로그래밍
자바와 관련된 책이군요
자바와 관련된 책이군요
```

---

### 6) split() - 문자열 분리

**구분자를 기준**으로 문자열을 분리하여 **배열로 반환**합니다.

```
package ch05.sec05;

public class SplitExample {
    public static void main(String[] args) {
        String board = "1,자바 학습,참조 타입 String을 학습합니다.,홍길동";

        // 쉼표(,)를 기준으로 분리
        String[] tokens = board.split(",");

        System.out.println("번호: " + tokens[0]);
        System.out.println("제목: " + tokens[1]);
        System.out.println("내용: " + tokens[2]);
        System.out.println("성명: " + tokens[3]);
        System.out.println();

        for (int i = 0; i < tokens.length; i++) {
            System.out.println(tokens[i]);
        }
    }
}
```

**실행 결과:**

```
번호: 1
제목: 자바 학습
내용: 참조 타입 String을 학습합니다.
성명: 홍길동

1
자바 학습
참조 타입 String을 학습합니다.
홍길동
```

### String 메소드 정리

| 메소드 | 기능 | 반환 타입 |
| --- | --- | --- |
| `charAt(int index)` | 특정 인덱스의 문자 추출 | char |
| `length()` | 문자열 길이 | int |
| `replace(old, new)` | 문자열 치환 | String |
| `substring(start, end)` | 문자열 자르기 | String |
| `indexOf(str)` | 문자열 위치 찾기 | int |
| `contains(str)` | 포함 여부 확인 | boolean |
| `split(regex)` | 문자열 분리 | String\[\] |
| `equals(str)` | 내용 비교 | boolean |
| `toUpperCase()` | 대문자 변환 | String |
| `toLowerCase()` | 소문자 변환 | String |
| `trim()` | 양쪽 공백 제거 | String |

---

## 8\. 배열 (Array)

### 배열이란?

**같은 타입의 데이터를 연속된 공간에 저장**하는 자료구조입니다.

**배열의 특징:**

-   같은 타입의 데이터만 저장 가능
-   한 번 생성하면 **크기 변경 불가**
-   인덱스로 접근 (0부터 시작)

### 배열 변수 선언

```
// 방법 1 (권장)
int[] scores;
String[] names;

// 방법 2
int scores[];
String names[];
```

---

## 9\. 배열 생성 방법

### 1) 값 목록으로 배열 생성

```
package ch05.sec06;

public class ArrayCreateByValueListExample {
    public static void main(String[] args) {
        // 배열 선언과 동시에 초기화
        String[] season = {"Spring", "Summer", "Fall", "Winter"};

        System.out.println("season[0]: " + season[0]);
        System.out.println("season[1]: " + season[1]);
        System.out.println("season[2]: " + season[2]);
        System.out.println("season[3]: " + season[3]);

        // 배열 항목 값 변경
        season[1] = "여름";
        System.out.println("season[1]: " + season[1]);
        System.out.println();

        // 배열 활용 - 합계와 평균
        int[] scores = {83, 90, 87};

        int sum = 0;
        for (int i = 0; i < 3; i++) {
            sum += scores[i];
        }
        System.out.println("총합: " + sum);

        double avg = (double) sum / 3;
        System.out.println("평균: " + avg);
    }
}
```

**실행 결과:**

```
season[0]: Spring
season[1]: Summer
season[2]: Fall
season[3]: Winter
season[1]: 여름

총합: 260
평균: 86.66666666666667
```

### 배열 변수를 먼저 선언하는 경우

선언과 초기화를 분리할 때는 **`new 타입[]`** 필요합니다.

```
package ch05.sec06;

public class ArrayCreateByValueListExample2 {
    public static void main(String[] args) {
        int[] scores;
        scores = new int[] {83, 90, 87};  // new 연산자 필요!

        int sum1 = 0;
        for (int i = 0; i < 3; i++) {
            sum1 += scores[i];
        }
        System.out.println("총합: " + sum1);

        // 메소드 매개값으로 배열 전달
        printItem(new int[] {83, 90, 87});
    }

    public static void printItem(int[] scores) {
        for (int i = 0; i < 3; i++) {
            System.out.println("score[" + i + "]: " + scores[i]);
        }
    }
}
```

**실행 결과:**

```
총합: 260
score[0]: 83
score[1]: 90
score[2]: 87
```

---

### 2) new 연산자로 배열 생성

크기만 지정하고 나중에 값을 할당할 수 있습니다.

```
package ch05.sec06;

public class ArrayCreateByNewExample {
    public static void main(String[] args) {
        // int 배열 생성
        int[] arr1 = new int[3];

        for (int i = 0; i < 3; i++) {
            System.out.print("arr1[" + i + "]: " + arr1[i] + ", ");
        }
        System.out.println();

        arr1[0] = 10;
        arr1[1] = 20;
        arr1[2] = 30;

        for (int i = 0; i < 3; i++) {
            System.out.print("arr1[" + i + "]: " + arr1[i] + ", ");
        }
        System.out.println("\n");

        // double 배열 생성
        double[] arr2 = new double[3];

        for (int i = 0; i < 3; i++) {
            System.out.print("arr2[" + i + "]: " + arr2[i] + ", ");
        }
        System.out.println();

        arr2[0] = 0.1;
        arr2[1] = 0.2;
        arr2[2] = 0.3;

        for (int i = 0; i < 3; i++) {
            System.out.print("arr2[" + i + "]: " + arr2[i] + ", ");
        }
        System.out.println("\n");

        // String 배열 생성
        String[] arr3 = new String[3];

        for (int i = 0; i < 3; i++) {
            System.out.print("arr3[" + i + "]: " + arr3[i] + ", ");
        }
        System.out.println();

        arr3[0] = "1월";
        arr3[1] = "2월";
        arr3[2] = "3월";

        for (int i = 0; i < 3; i++) {
            System.out.print("arr3[" + i + "]: " + arr3[i] + ", ");
        }
        System.out.println();
    }
}
```

**실행 결과:**

```
arr1[0]: 0, arr1[1]: 0, arr1[2]: 0, 
arr1[0]: 10, arr1[1]: 20, arr1[2]: 30, 

arr2[0]: 0.0, arr2[1]: 0.0, arr2[2]: 0.0, 
arr2[0]: 0.1, arr2[1]: 0.2, arr2[2]: 0.3, 

arr3[0]: null, arr3[1]: null, arr3[2]: null, 
arr3[0]: 1월, arr3[1]: 2월, arr3[2]: 3월, 
```

### 배열의 기본값

new 연산자로 배열 생성 시 자동으로 **기본값**이 할당됩니다.

| 타입 | 기본값 |
| --- | --- |
| byte, short, int, long | 0 |
| float, double | 0.0 |
| char | '\\u0000' |
| boolean | false |
| 참조 타입 (String 등) | null |

---

## 10\. 배열 길이

### length 필드

배열의 길이는 `배열변수.length`로 얻을 수 있습니다.

```
package ch05.sec06;

public class ArrayLengthExample {
    public static void main(String[] args) {
        int[] scores = {84, 90, 96};

        int sum = 0;
        for (int i = 0; i < scores.length; i++) {  // length 사용
            sum += scores[i];
        }

        System.out.println("총합: " + sum);

        double avg = (double) sum / scores.length;
        System.out.println("평균: " + avg);
    }
}
```

**실행 결과:**

```
총합: 270
평균: 90.0
```

> **장점:** 배열 길이가 변경되어도 코드 수정 불필요!

---

## 11\. 다차원 배열

### 다차원 배열이란?

배열의 항목에 **또 다른 배열**을 저장하는 구조입니다.

**2차원 배열 구조:**

```
scores[0] → [80, 90, 96]
scores[1] → [76, 88]
```

### 1) 값 목록으로 다차원 배열 생성

```
package ch05.sec07;

public class MultidimensionalArrayByValueExample {
    public static void main(String[] args) {
        int[][] scores = {
            {80, 90, 96},  // 첫 번째 반
            {76, 88}       // 두 번째 반
        };

        System.out.println("1차원 배열 길이(반의 수): " + scores.length);
        System.out.println("2차원 배열 길이(첫 번째 반의 학생 수): " + scores[0].length);
        System.out.println("2차원 배열 길이(두 번째 반의 학생 수): " + scores[1].length);

        System.out.println("scores[0][2]: " + scores[0][2]);
        System.out.println("scores[1][1]: " + scores[1][1]);

        // 첫 번째 반 평균
        int class1Sum = 0;
        for (int i = 0; i < scores[0].length; i++) {
            class1Sum += scores[0][i];
        }
        double class1Avg = (double) class1Sum / scores[0].length;
        System.out.println("첫 번째 반의 평균: " + class1Avg);

        // 두 번째 반 평균
        int class2Sum = 0;
        for (int i = 0; i < scores[1].length; i++) {
            class2Sum += scores[1][i];
        }
        double class2Avg = (double) class2Sum / scores[1].length;
        System.out.println("두 번째 반의 평균: " + class2Avg);

        // 전체 학생 평균
        int totalStudent = 0;
        int totalSum = 0;
        for (int i = 0; i < scores.length; i++) {
            totalStudent += scores[i].length;
            for (int k = 0; k < scores[i].length; k++) {
                totalSum += scores[i][k];
            }
        }
        double totalAvg = (double) totalSum / totalStudent;
        System.out.println("전체 학생의 평균 점수: " + totalAvg);
    }
}
```

**실행 결과:**

```
1차원 배열 길이(반의 수): 2
2차원 배열 길이(첫 번째 반의 학생 수): 3
2차원 배열 길이(두 번째 반의 학생 수): 2
scores[0][2]: 96
scores[1][1]: 88
첫 번째 반의 평균: 88.66666666666667
두 번째 반의 평균: 82.0
전체 학생의 평균 점수: 86.0
```

---

### 2) new 연산자로 다차원 배열 생성

#### 정방 배열 (각 행의 길이가 같음)

```
int[][] arr = new int[2][3];  // 2행 3열
```

#### 가변 배열 (각 행의 길이가 다름)

```
int[][] arr = new int[2][];  // 행만 먼저 생성
arr[0] = new int[2];          // 첫 번째 행: 2개
arr[1] = new int[3];          // 두 번째 행: 3개
```

```
package ch05.sec07;

public class MultidimensionalArrayByNewExample {
    public static void main(String[] args) {
        // 정방 배열 (2행 3열)
        int[][] mathScores = new int[2][3];

        for (int i = 0; i < mathScores.length; i++) {
            for (int k = 0; k < mathScores[i].length; k++) {
                System.out.println("mathScores[" + i + "][" + k + "]: " + mathScores[i][k]);
            }
        }
        System.out.println();

        mathScores[0][0] = 80;
        mathScores[0][1] = 83;
        mathScores[0][2] = 85;
        mathScores[1][0] = 86;
        mathScores[1][1] = 90;
        mathScores[1][2] = 92;

        int totalStudent = 0;
        int totalMathSum = 0;
        for (int i = 0; i < mathScores.length; i++) {
            totalStudent += mathScores[i].length;
            for (int k = 0; k < mathScores[i].length; k++) {
                totalMathSum += mathScores[i][k];
            }
        }
        double totalMathAvg = (double) totalMathSum / totalStudent;
        System.out.println("전체 학생의 수학 평균 점수: " + totalMathAvg);
        System.out.println();

        // 가변 배열
        int[][] englishScores = new int[2][];
        englishScores[0] = new int[2];  // 첫 번째 반: 2명
        englishScores[1] = new int[3];  // 두 번째 반: 3명

        for (int i = 0; i < englishScores.length; i++) {
            for (int k = 0; k < englishScores[i].length; k++) {
                System.out.println("englishScores[" + i + "][" + k + "]: " + englishScores[i][k]);
            }
        }
        System.out.println();

        englishScores[0][0] = 90;
        englishScores[0][1] = 91;
        englishScores[1][0] = 92;
        englishScores[1][1] = 93;
        englishScores[1][2] = 94;

        totalStudent = 0;
        int totalEnglishSum = 0;
        for (int i = 0; i < englishScores.length; i++) {
            totalStudent += englishScores[i].length;
            for (int k = 0; k < englishScores[i].length; k++) {
                totalEnglishSum += englishScores[i][k];
            }
        }
        double totalEnglishAvg = (double) totalEnglishSum / totalStudent;
        System.out.println("전체 학생의 영어 평균 점수: " + totalEnglishAvg);
    }
}
```

**실행 결과:**

```
mathScores[0][0]: 0
mathScores[0][1]: 0
mathScores[0][2]: 0
mathScores[1][0]: 0
mathScores[1][1]: 0
mathScores[1][2]: 0

전체 학생의 수학 평균 점수: 86.0

englishScores[0][0]: 0
englishScores[0][1]: 0
englishScores[1][0]: 0
englishScores[1][1]: 0
englishScores[1][2]: 0

전체 학생의 영어 평균 점수: 92.0
```

---

## 12\. 객체를 참조하는 배열

### 참조 타입 배열

기본 타입 배열은 **값**을 직접 저장하지만, 참조 타입 배열은 **객체의 주소**를 저장합니다.

```
package ch05.sec08;

public class ArrayReferenceObjectExample {
    public static void main(String[] args) {
        String[] strArray = new String[3];
        strArray[0] = "Java";           // String Pool의 "Java"
        strArray[1] = "Java";           // String Pool의 같은 "Java"
        strArray[2] = new String("Java"); // 힙에 새로운 "Java"

        System.out.println(strArray[0] == strArray[1]);      // true
        System.out.println(strArray[0] == strArray[2]);      // false
        System.out.println(strArray[0].equals(strArray[2])); // true
    }
}
```

**실행 결과:**

```
true
false
true
```

### 메모리 구조

```
strArray (Stack)
┌────┐
│ 0  │ ──→ "Java" (String Pool)
├────┤       ↑
│ 1  │ ──────┘
├────┤
│ 2  │ ──→ "Java" (Heap, 새 객체)
└────┘
```

---

## 13\. 배열 복사

### 배열의 크기는 변경 불가

배열은 생성 후 **크기를 변경할 수 없습니다**. 더 큰 배열이 필요하면 **새로 생성**하고 **복사**해야 합니다.

### 1) for 문으로 복사

```
package ch05.sec09;

public class ArrayCopyByForExample {
    public static void main(String[] args) {
        int[] oldIntArray = {1, 2, 3};

        int[] newIntArray = new int[5];

        for (int i = 0; i < oldIntArray.length; i++) {
            newIntArray[i] = oldIntArray[i];
        }

        for (int i = 0; i < newIntArray.length; i++) {
            System.out.print(newIntArray[i] + ", ");
        }
    }
}
```

**실행 결과:**

```
1, 2, 3, 0, 0, 
```

---

### 2) System.arraycopy() 메소드

더 효율적인 배열 복사 방법입니다.

**문법:**

```
System.arraycopy(원본배열, 원본시작인덱스, 대상배열, 대상시작인덱스, 복사개수);
```

```
package ch05.sec09;

public class ArrayCopyExample {
    public static void main(String[] args) {
        String[] oldStrArray = {"java", "array", "copy"};

        String[] newStrArray = new String[5];

        System.arraycopy(oldStrArray, 0, newStrArray, 0, oldStrArray.length);

        for (int i = 0; i < newStrArray.length; i++) {
            System.out.print(newStrArray[i] + ", ");
        }
    }
}
```

**실행 결과:**

```
java, array, copy, null, null, 
```

> **권장:** 배열 복사 시 `System.arraycopy()` 사용 (성능이 더 좋음)

---

## 14\. 향상된 for 문 (Enhanced for)

### 향상된 for 문이란?

배열이나 컬렉션을 **간편하게 순회**할 수 있는 for 문입니다.

**문법:**

```
for (타입 변수 : 배열) {
    // 변수에 배열 항목이 순서대로 대입됨
}
```

```
package ch05.sec10;

public class AdvancedForExample {
    public static void main(String[] args) {
        int[] scores = {95, 71, 84, 93, 87};

        int sum = 0;
        for (int score : scores) {  // scores의 각 항목이 score에 대입
            sum = sum + score;
        }

        System.out.println("점수 총합 = " + sum);

        double avg = (double) sum / scores.length;
        System.out.println("점수 평균 = " + avg);
    }
}
```

**실행 결과:**

```
점수 총합 = 430
점수 평균 = 86.0
```

### 일반 for vs 향상된 for

```
// 일반 for (인덱스 필요)
for (int i = 0; i < scores.length; i++) {
    System.out.println(scores[i]);
}

// 향상된 for (값만 필요)
for (int score : scores) {
    System.out.println(score);
}
```

> **사용 시점:**
> 
> -   인덱스가 필요하면 → 일반 for
> -   값만 필요하면 → 향상된 for

---

## 15\. main() 메소드의 매개변수

### String\[\] args란?

프로그램 실행 시 **명령줄 인수(command line arguments)**를 받기 위한 매개변수입니다.

```
package ch05.sec11;

public class MainStringArrayArgument {
    public static void main(String[] args) {
        if (args.length != 2) {
            System.out.println("프로그램 입력값이 부족");
            System.exit(0);
        }

        String strNum1 = args[0];
        String strNum2 = args[1];

        int num1 = Integer.parseInt(strNum1);
        int num2 = Integer.parseInt(strNum2);

        int result = num1 + num2;
        System.out.println(num1 + " + " + num2 + " = " + result);
    }
}
```

### 실행 방법

**명령줄에서:**

```
java MainStringArrayArgument 10 20
```

**실행 결과:**

```
10 + 20 = 30
```

**인수가 부족한 경우:**

```
java MainStringArrayArgument
```

**실행 결과:**

```
프로그램 입력값이 부족
```

---

## 16\. 열거 타입 (Enum)

### 열거 타입이란?

**한정된 값들의 집합**을 정의하는 타입입니다.

**사용 이유:**

-   코드 가독성 향상
-   잘못된 값 입력 방지
-   타입 안전성 보장

### 열거 타입 정의

**Week.java** (별도 파일로 생성)

```
package ch05.sec12;

public enum Week {
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```

### 열거 타입 사용

```
package ch05.sec12;

import java.util.Calendar;

public class WeekExample {
    public static void main(String[] args) {
        Week today = null;

        // 현재 날짜와 시간 정보를 가진 Calendar 객체 얻기
        Calendar cal = Calendar.getInstance();

        // 오늘의 요일 가져오기 (1: 일요일 ~ 7: 토요일)
        int week = cal.get(Calendar.DAY_OF_WEEK);

        switch (week) {
            case 1: today = Week.SUNDAY;    break;
            case 2: today = Week.MONDAY;    break;
            case 3: today = Week.TUESDAY;   break;
            case 4: today = Week.WEDNESDAY; break;
            case 5: today = Week.THURSDAY;  break;
            case 6: today = Week.FRIDAY;    break;
            case 7: today = Week.SATURDAY;  break;
        }

        if (today == Week.SUNDAY) {
            System.out.println("일요일에는 축구를 합니다.");
        } else {
            System.out.println("열심히 자바를 공부합니다.");
        }
    }
}
```

**실행 결과:**

```
열심히 자바를 공부합니다.
```

### 열거 타입의 장점

#### 1) 타입 안전성

```
// 열거 타입 사용 (안전)
Week today = Week.MONDAY;

// 잘못된 값 할당 불가
// Week today = "MONDAY";  // 컴파일 에러!
```

#### 2) 코드 가독성

```
// int 사용 (가독성 낮음)
int day = 1;
if (day == 1) { ... }  // 1이 무엇을 의미하는지 불명확

// enum 사용 (가독성 높음)
Week day = Week.MONDAY;
if (day == Week.MONDAY) { ... }  // 명확!
```

#### 3) 자동완성 지원

IDE에서 열거 상수를 자동으로 제안합니다.

### 열거 타입 메소드

```
Week today = Week.MONDAY;

// name(): 열거 상수의 이름 반환
String name = today.name();  // "MONDAY"

// ordinal(): 열거 상수의 순번 반환 (0부터 시작)
int ordinal = today.ordinal();  // 0

// values(): 모든 열거 상수를 배열로 반환
Week[] days = Week.values();
for (Week day : days) {
    System.out.println(day);
}
```

---

## 📌 정리

### 기본 타입 vs 참조 타입

| 구분 | 기본 타입 | 참조 타입 |
| --- | --- | --- |
| 저장 내용 | 실제 값 | 객체 주소 |
| 메모리 | 스택 | 힙 |
| 기본값 | 0, false | null |
| 비교 | 값 비교 | 주소 비교 |

### 메모리 영역

-   **메소드 영역**: 클래스 정보, static 변수
-   **힙 영역**: 객체, 배열 (GC 대상)
-   **스택 영역**: 지역 변수, 메소드 프레임

### String 특징

-   **String Pool**: 리터럴은 재사용
-   **불변성(Immutable)**: 변경 시 새 객체 생성
-   **비교**: `==` (주소), `equals()` (내용)

### 배열

-   **고정 크기**: 생성 후 크기 변경 불가
-   **인덱스**: 0부터 시작
-   **length**: 배열 길이
-   **향상된 for**: 순회 간편화

### 열거 타입

-   **한정된 값**: 정해진 상수만 사용
-   **타입 안전**: 잘못된 값 방지
-   **가독성**: 코드 의미 명확

### 주의사항

1.  **NullPointerException**: null 체크 필수
2.  **배열 인덱스**: 범위 주의 (0 ~ length-1)
3.  **String 비교**: `equals()` 사용
4.  **배열 복사**: `System.arraycopy()` 권장
5.  **가비지 컬렉션**: 참조가 없으면 자동 제거

---