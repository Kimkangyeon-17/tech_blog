# Java 기초 - 클래스와 객체 지향 프로그래밍

## 1. 객체 지향 프로그래밍 (OOP)

### 객체 지향 프로그래밍이란?

**객체 지향 프로그래밍(Object-Oriented Programming, OOP)**은 현실 세계의 사물(객체)을 프로그램으로 모델링하는 프로그래밍 방식입니다.

**객체(Object)란?**
- **속성(필드)**: 객체가 가지는 데이터
- **동작(메소드)**: 객체가 수행하는 기능

**예시:**
```
자동차 객체
├── 속성: 색상, 모델, 속도
└── 동작: 시동켜기(), 가속하기(), 브레이크()
```

---

## 2. 객체 지향의 3대 특성

### 1) 캡슐화 (Encapsulation)

**캡슐화란?**
- 객체의 **데이터(필드)**와 **동작(메소드)**을 하나로 묶는 것
- 내부 구현을 외부에 **감추고(정보 은닉)**, 필요한 부분만 **공개**

**목적:**
- 외부의 잘못된 사용으로부터 객체 보호
- 객체의 무결성 유지

**구현 방법:**
- **접근 제한자** 사용 (`private`, `public` 등)

```java
public class Account {
    private int balance;  // 외부에서 직접 접근 불가
    
    // public 메소드를 통해서만 접근 가능
    public void deposit(int amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    public int getBalance() {
        return balance;
    }
}
```

---

### 2) 상속 (Inheritance)

**상속이란?**
- 부모 클래스의 **필드와 메소드**를 자식 클래스가 물려받는 것

**목적:**
- **코드 재사용성** 향상
- **유지보수 시간** 단축

**관계:**
```
부모 클래스 (상위 클래스, Super Class)
    ↓ extends
자식 클래스 (하위 클래스, Sub Class)
```

**예시:**
```java
// 부모 클래스
class Animal {
    String name;
    
    void eat() {
        System.out.println(name + "이(가) 먹습니다.");
    }
}

// 자식 클래스
class Dog extends Animal {
    void bark() {
        System.out.println(name + "이(가) 짖습니다.");
    }
}

// 사용
Dog dog = new Dog();
dog.name = "멍멍이";
dog.eat();   // 부모에게서 상속받은 메소드
dog.bark();  // 자신의 메소드
```

---

### 3) 다형성 (Polymorphism)

**다형성이란?**
- 같은 메소드 호출이지만 **실행 결과가 다양**하게 나오는 것

**구현 기술:**
- **오버라이딩(Overriding)**: 부모 메소드 재정의
- **자동 타입 변환**: 부모 타입으로 자식 객체 참조

**예시:**
```java
class Animal {
    void sound() {
        System.out.println("동물 소리");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("멍멍!");
    }
}

class Cat extends Animal {
    @Override
    void sound() {
        System.out.println("야옹!");
    }
}

// 사용 - 같은 메소드 호출, 다른 결과
Animal animal1 = new Dog();
Animal animal2 = new Cat();

animal1.sound();  // 멍멍!
animal2.sound();  // 야옹!
```

---

## 3. 클래스 선언

### 클래스란?

**클래스(Class)**는 객체를 생성하기 위한 **설계도** 또는 **틀**입니다.

**비유:**
```
붕어빵 틀(클래스) → 붕어빵(객체)
자동차 설계도(클래스) → 실제 자동차(객체)
```

### 클래스 선언 방법

**파일명: ClassName.java**
```java
public class ClassName {
    // 필드 (속성)
    // 생성자 (객체 초기화)
    // 메소드 (동작)
}
```

**규칙:**
- 클래스명은 **대문자**로 시작 (PascalCase)
- 파일명과 클래스명은 **동일**해야 함
- `public` 클래스는 **파일당 하나**만 선언 가능

**예시:**
```java
// Student.java
public class Student {
    String name;
    int age;
    
    void study() {
        System.out.println(name + "이(가) 공부합니다.");
    }
}
```

---

## 4. 객체 생성과 사용

### 객체 생성

**`new` 연산자**를 사용하여 객체를 생성합니다.

```java
클래스명 변수명 = new 클래스명();
```

**Student.java**
```java
package ch06.sec04;

public class Student {
    // 클래스 내용은 비어있어도 됨
}
```

**StudentExample.java**
```java
package ch06.sec04;

public class StudentExample {
    public static void main(String[] args) {
        // 첫 번째 Student 객체 생성
        Student s1 = new Student();
        System.out.println("s1 변수가 Student 객체를 참조합니다.");
        
        // 두 번째 Student 객체 생성
        Student s2 = new Student();
        System.out.println("s2 변수가 또 다른 Student 객체를 참조합니다.");
    }
}
```

**실행 결과:**
```bash
s1 변수가 Student 객체를 참조합니다.
s2 변수가 또 다른 Student 객체를 참조합니다.
```

### 메모리 구조

```
Stack 영역           Heap 영역
┌──────────┐        ┌─────────────┐
│ s1  ──→  │        │ Student 객체 │
└──────────┘        └─────────────┘
┌──────────┐        ┌─────────────┐
│ s2  ──→  │        │ Student 객체 │
└──────────┘        └─────────────┘
```

> **핵심:** `new` 연산자는 **새로운 객체**를 힙 영역에 생성하고, 그 주소를 반환합니다.

---

## 5. 클래스의 구성 멤버

### 클래스 구성 요소

```java
public class ClassName {
    // 1. 필드 (Field) - 객체의 데이터
    int field1;
    String field2;
    
    // 2. 생성자 (Constructor) - 객체 초기화
    ClassName() {
        // 초기화 코드
    }
    
    // 3. 메소드 (Method) - 객체의 동작
    void method1() {
        // 실행 코드
    }
    
    int method2(int x) {
        return x * 2;
    }
}
```

| 구성 멤버 | 역할 | 특징 |
|----------|------|------|
| **필드** | 객체의 데이터 저장 | 변수와 비슷하지만 클래스 블록에 선언 |
| **생성자** | 객체 초기화 | 리턴 타입 없음, 클래스명과 동일 |
| **메소드** | 객체의 동작 정의 | 다른 언어의 함수와 유사 |

---

## 6. 필드 (Field)

### 필드 선언

**문법:**
```java
타입 필드명 [= 초기값];
```

**Car.java**
```java
package ch06.sec06.exam01;

public class Car {
    // 필드 선언 (초기값 없음)
    String model;
    boolean start;
    int speed;
}
```

**CarExample.java**
```java
package ch06.sec06.exam01;

public class CarExample {
    public static void main(String[] args) {
        Car myCar = new Car();
        
        // 필드 읽기 (기본값 출력)
        System.out.println("모델명: " + myCar.model);
        System.out.println("시동 여부: " + myCar.start);
        System.out.println("현재 속도: " + myCar.speed);
    }
}
```

**실행 결과:**
```bash
모델명: null
시동 여부: false
현재 속도: 0
```

### 필드 기본값

필드는 초기값을 주지 않아도 **자동으로 기본값**이 할당됩니다.

| 타입 | 기본값 |
|------|--------|
| byte, short, int, long | 0 |
| float, double | 0.0 |
| char | '\u0000' |
| boolean | false |
| 참조 타입 | null |

---

### 필드 초기화

**Car.java**
```java
package ch06.sec06.exam02;

public class Car {
    // 필드 선언 시 초기값 대입
    String company = "현대";
    String model = "그랜저";
    String color = "검정";
    int maxSpeed = 350;
    int speed;  // 0으로 자동 초기화
}
```

**CarExample.java**
```java
package ch06.sec06.exam02;

public class CarExample {
    public static void main(String[] args) {
        Car myCar = new Car();

        System.out.println("제작 회사: " + myCar.company);
        System.out.println("모델명: " + myCar.model);
        System.out.println("색깔: " + myCar.color);
        System.out.println("최고속도: " + myCar.maxSpeed);
        System.out.println("현재속도: " + myCar.speed);
        
        // 필드 값 변경
        myCar.speed = 60;
        System.out.println("수정된 속도: " + myCar.speed);
    }
}
```

**실행 결과:**
```bash
제작 회사: 현대
모델명: 그랜저
색깔: 검정
최고속도: 350
현재속도: 0
수정된 속도: 60
```

---

## 7. 생성자 (Constructor)

### 생성자란?

**생성자**는 `new` 연산자로 객체를 생성할 때 **객체를 초기화**하는 특별한 메소드입니다.

**특징:**
- **리턴 타입이 없음** (void도 작성하지 않음)
- 이름이 **클래스명과 동일**
- 여러 개 선언 가능 (오버로딩)

### 기본 생성자

클래스에 생성자가 하나도 없으면 컴파일러가 **기본 생성자**를 자동 추가합니다.

```java
// 개발자가 작성하지 않아도 자동 추가됨
public ClassName() {
}
```

> **주의:** 생성자를 하나라도 선언하면 기본 생성자는 **자동 추가되지 않습니다!**

---

### 생성자 선언

**Car.java**
```java
package ch06.sec07.exam01;

public class Car {
    // 생성자 선언
    Car(String model, String color, int maxSpeed) {
        // 객체 초기화 코드
    }
}
```

**CarExample.java**
```java
package ch06.sec07.exam01;

public class CarExample {
    public static void main(String[] args) {
        // 생성자 호출
        Car myCar = new Car("그랜저", "검정", 250);
    }
}
```

---

### 필드 초기화

**Korean.java**
```java
package ch06.sec07.exam02;

public class Korean {
    // 필드
    String nation = "대한민국";  // 모든 객체가 동일한 값
    String name;                 // 객체마다 다른 값
    String ssn;                  // 객체마다 다른 값
    
    // 생성자
    public Korean(String n, String s) {
        name = n;
        ssn = s;
    }
}
```

**KoreanExample.java**
```java
package ch06.sec07.exam02;

public class KoreanExample {
    public static void main(String[] args) {
        Korean k1 = new Korean("박자바", "011223-1234567");
        
        System.out.println("k1.nation: " + k1.nation);
        System.out.println("k1.name: " + k1.name);
        System.out.println("k1.ssn: " + k1.ssn);
        System.out.println();
        
        Korean k2 = new Korean("김자바", "991122-1234567");
        
        System.out.println("k2.nation: " + k2.nation);
        System.out.println("k2.name: " + k2.name);
        System.out.println("k2.ssn: " + k2.ssn);
    }
}
```

**실행 결과:**
```bash
k1.nation: 대한민국
k1.name: 박자바
k1.ssn: 011223-1234567

k2.nation: 대한민국
k2.name: 김자바
k2.ssn: 991122-1234567
```

### 초기화 가이드

| 상황 | 초기화 방법 |
|------|------------|
| **모든 객체가 동일한 값** | 필드 선언 시 초기값 대입 |
| **객체마다 다른 값** | 생성자에서 초기화 |

---

### 생성자 오버로딩 (Constructor Overloading)

**생성자 오버로딩**은 **매개변수를 다르게** 하여 여러 개의 생성자를 선언하는 것입니다.

**목적:** 다양한 방법으로 객체를 생성하기 위함

**Car.java**
```java
package ch06.sec07.exam04;

public class Car {
    String company = "현대";
    String model;
    String color;
    int maxSpeed;
    
    // 기본 생성자
    Car() {
    }
    
    // 매개변수 1개
    Car(String model) {
        this.model = model;
    }
    
    // 매개변수 2개
    Car(String model, String color) {
        this.model = model;
        this.color = color;
    }
    
    // 매개변수 3개
    Car(String model, String color, int maxSpeed) {
        this.model = model;
        this.color = color;
        this.maxSpeed = maxSpeed;
    }
}
```

**CarExample.java**
```java
package ch06.sec07.exam04;

public class CarExample {
    public static void main(String[] args) {
        Car car1 = new Car();
        System.out.println("car1.company: " + car1.company);
        System.out.println();
        
        Car car2 = new Car("자가용");
        System.out.println("car2.company: " + car2.company);
        System.out.println("car2.model: " + car2.model);
        System.out.println();
        
        Car car3 = new Car("자가용", "빨강");
        System.out.println("car3.company: " + car3.company);
        System.out.println("car3.model: " + car3.model);
        System.out.println("car3.color: " + car3.color);
        System.out.println();

        Car car4 = new Car("택시", "검정", 200);
        System.out.println("car4.company: " + car4.company);
        System.out.println("car4.model: " + car4.model);
        System.out.println("car4.color: " + car4.color);
        System.out.println("car4.maxSpeed: " + car4.maxSpeed);
    }
}
```

**실행 결과:**
```bash
car1.company: 현대

car2.company: 현대
car2.model: 자가용

car3.company: 현대
car3.model: 자가용
car3.color: 빨강

car4.company: 현대
car4.model: 택시
car4.color: 검정
car4.maxSpeed: 200
```

---

### this 키워드

**`this`**는 **현재 객체**를 참조하는 키워드입니다.

**사용 목적:**
1. **필드와 매개변수 구분**
2. **다른 생성자 호출**

#### 1) 필드와 매개변수 구분

```java
class Person {
    String name;
    
    // 매개변수명과 필드명이 같을 때
    Person(String name) {
        this.name = name;  // this.name은 필드, name은 매개변수
    }
}
```

#### 2) 다른 생성자 호출

**`this()`**로 같은 클래스의 다른 생성자를 호출할 수 있습니다.

```java
class Car {
    String model;
    String color;
    int maxSpeed;
    
    Car(String model) {
        this(model, "은색", 250);  // 다른 생성자 호출
    }
    
    Car(String model, String color) {
        this(model, color, 250);  // 다른 생성자 호출
    }
    
    Car(String model, String color, int maxSpeed) {
        this.model = model;
        this.color = color;
        this.maxSpeed = maxSpeed;
    }
}
```

> **주의:** `this()`는 **생성자의 첫 줄**에만 올 수 있습니다!

---

## 8. 메소드 (Method)

### 메소드란?

**메소드**는 객체의 **동작(기능)**을 정의한 것입니다.

**역할:**
- 객체 내부에서 실행
- 객체 간 상호작용 (다른 객체의 메소드 호출)

### 메소드 선언

**문법:**
```java
리턴타입 메소드명(매개변수, ...) {
    // 실행 블록
    return 리턴값;
}
```

**구성 요소:**

| 요소 | 설명 |
|------|------|
| **리턴 타입** | 메소드가 반환하는 값의 타입 (없으면 `void`) |
| **메소드명** | 소문자로 시작, camelCase 사용 |
| **매개변수** | 메소드 호출 시 전달받는 값 |
| **실행 블록** | 메소드가 수행할 코드 |
| **return** | 값을 반환하고 메소드 종료 |

---

### 메소드 호출

**Calculator.java**
```java
package ch06.sec08.exam01;

public class Calculator {
    // 리턴값 없음
    void powerOn() {
        System.out.println("전원을 켭니다.");
    }
    
    void powerOff() {
        System.out.println("전원을 끕니다.");
    }
    
    // 리턴값 있음 (int)
    int plus(int x, int y) {
        int result = x + y;
        return result;
    }
    
    // 리턴값 있음 (double)
    double divide(int x, int y) {
        double result = (double) x / (double) y;
        return result;
    }
}
```

**CalculatorExample.java**
```java
package ch06.sec08.exam01;

public class CalculatorExample {
    public static void main(String[] args) {
        Calculator myCalc = new Calculator();
        
        // 리턴값 없는 메소드 호출
        myCalc.powerOn();
        
        // 리턴값 있는 메소드 호출
        int result1 = myCalc.plus(5, 6);
        System.out.println("result1: " + result1);
        
        int x = 10;
        int y = 4;
        
        double result2 = myCalc.divide(x, y);
        System.out.println("result2: " + result2);
        
        myCalc.powerOff();
    }
}
```

**실행 결과:**
```bash
전원을 켭니다.
result1: 11
result2: 2.5
전원을 끕니다.
```

---

### 가변길이 매개변수

**`타입... 변수명`** 형태로 선언하면 **개수 제한 없이** 매개값을 받을 수 있습니다.

**Computer.java**
```java
package ch06.sec08.exam02;

public class Computer {
    // 가변길이 매개변수
    int sum(int... values) {
        int sum = 0;
        
        // values는 배열로 처리됨
        for (int i = 0; i < values.length; i++) {
            sum += values[i];
        }
        
        return sum;
    }
}
```

**ComputerExample.java**
```java
package ch06.sec08.exam02;

public class ComputerExample {
    public static void main(String[] args) {
        Computer myCom = new Computer();
        
        // 매개값 3개
        int result1 = myCom.sum(1, 2, 3);
        System.out.println("result1: " + result1);
        
        // 매개값 5개
        int result2 = myCom.sum(1, 2, 3, 4, 5);
        System.out.println("result2: " + result2);
        
        // 배열로 전달
        int[] values = {1, 2, 3, 4, 5};
        int result3 = myCom.sum(values);
        System.out.println("result3: " + result3);
        
        // new 배열로 전달
        int result4 = myCom.sum(new int[] {1, 2, 3, 4, 5});
        System.out.println("result4: " + result4);
    }
}
```

**실행 결과:**
```bash
result1: 6
result2: 15
result3: 15
result4: 15
```

---

### return 문

**`return`** 문은 메소드 실행을 **즉시 종료**하고 호출한 곳으로 돌아갑니다.

**용도:**
1. 값 반환
2. 메소드 강제 종료

```java
// 1. 값 반환
int plus(int x, int y) {
    return x + y;  // 결과값 반환 후 종료
}

// 2. 강제 종료 (void 메소드)
void divide(int x, int y) {
    if (y == 0) {
        return;  // 메소드 즉시 종료
    }
    System.out.println(x / y);
}
```

---

### 메소드 오버로딩 (Method Overloading)

**메소드 오버로딩**은 같은 이름의 메소드를 **매개변수를 다르게** 하여 여러 개 선언하는 것입니다.

**조건:**
1. 메소드명이 **동일**
2. 매개변수의 **타입, 개수, 순서** 중 하나가 달라야 함
3. 리턴 타입은 **무관** (리턴 타입만 다르면 오버로딩 아님)

**Calculator.java**
```java
package ch06.sec08.exam03;

public class Calculator {
    // 정사각형 넓이 (매개변수 1개)
    double areaRectangle(double width) {
        return width * width;
    }
    
    // 직사각형 넓이 (매개변수 2개)
    double areaRectangle(double width, double height) {
        return width * height;
    }
}
```

**CalculatorExample.java**
```java
package ch06.sec08.exam03;

public class CalculatorExample {
    public static void main(String[] args) {
        Calculator myCalc = new Calculator();
        
        // 정사각형 계산
        double result1 = myCalc.areaRectangle(10);
        
        // 직사각형 계산
        double result2 = myCalc.areaRectangle(10, 20);
        
        System.out.println("정사각형 넓이: " + result1);
        System.out.println("직사각형 넓이: " + result2);
    }
}
```

**실행 결과:**
```bash
정사각형 넓이: 100.0
직사각형 넓이: 200.0
```

### 오버로딩 예시

```java
// 올바른 오버로딩
void println(int x) { }
void println(String x) { }         // 타입 다름 ✅
void println(int x, int y) { }     // 개수 다름 ✅
void println(String x, int y) { }  // 순서 다름 ✅

// 잘못된 오버로딩
int plus(int x, int y) { }
double plus(int x, int y) { }  // 리턴 타입만 다름 ❌
```

---

## 9. 인스턴스 멤버와 정적 멤버

### 인스턴스 멤버

**인스턴스 멤버**는 객체 생성 후 사용할 수 있는 필드와 메소드입니다.

```java
class Car {
    int speed;  // 인스턴스 필드
    
    void run() {  // 인스턴스 메소드
        speed = 100;
    }
}

// 사용
Car myCar = new Car();  // 객체 생성 필수!
myCar.speed = 60;
myCar.run();
```

### this 키워드

**`this`**는 **현재 객체의 참조**입니다.

**용도:**
- 매개변수와 필드 구분
- 객체 자신의 다른 생성자/메소드 호출

```java
class Person {
    String name;
    
    Person(String name) {
        this.name = name;  // this.name은 필드
    }
    
    void introduce() {
        System.out.println("내 이름은 " + this.name);
    }
}
```

---

## 10. 정적 멤버 (Static Member)

### 정적 멤버란?

**정적 멤버**는 클래스에 **고정적으로 위치**하는 멤버로, **객체 생성 없이** 사용할 수 있습니다.

**특징:**
- **메소드 영역**에 저장
- **클래스 로딩** 시 생성
- **모든 객체가 공유**

### 정적 멤버 선언

**`static`** 키워드를 붙여 선언합니다.

```java
class Calculator {
    static double pi = 3.14159;  // 정적 필드
    
    static int plus(int x, int y) {  // 정적 메소드
        return x + y;
    }
}
```

### 정적 멤버 사용

**클래스명으로 접근**합니다.

```java
// 정적 필드 사용
double pi = Calculator.pi;

// 정적 메소드 호출
int result = Calculator.plus(10, 5);

// 객체로도 접근 가능 (권장하지 않음)
Calculator calc = new Calculator();
int result2 = calc.plus(10, 5);  // 경고 발생
```

---

### 정적 블록

**정적 블록**은 클래스가 메모리에 로딩될 때 **자동으로 실행**됩니다.

```java
class Database {
    static String url;
    static String username;
    
    // 정적 블록
    static {
        url = "jdbc:mysql://localhost:3306/mydb";
        username = "root";
        System.out.println("데이터베이스 설정 완료");
    }
}
```

---

### 정적 멤버 사용 제한

정적 메소드와 정적 블록에서는 **인스턴스 멤버를 사용할 수 없습니다**.

**이유:** 정적 멤버는 객체 없이도 실행되는데, 인스턴스 멤버는 객체가 있어야 하기 때문입니다.

```java
class Car {
    int speed;  // 인스턴스 필드
    
    static void run() {
        // speed = 100;  // 컴파일 에러! 인스턴스 필드 사용 불가
        // this.speed = 100;  // 컴파일 에러! this 사용 불가
    }
}
```

**해결 방법:**
```java
static void run(Car car) {
    car.speed = 100;  // 객체를 매개변수로 받으면 사용 가능
}
```

---

### 인스턴스 vs 정적 멤버

| 구분 | 인스턴스 멤버 | 정적 멤버 |
|------|--------------|----------|
| **선언** | `int field;` | `static int field;` |
| **메모리** | 힙 영역 | 메소드 영역 |
| **생성 시점** | 객체 생성 시 | 클래스 로딩 시 |
| **사용 방법** | `객체.멤버` | `클래스명.멤버` |
| **공유 여부** | 객체마다 독립적 | 모든 객체가 공유 |

**선택 가이드:**
- 객체마다 다른 데이터 → **인스턴스 멤버**
- 모든 객체가 공유하는 데이터 → **정적 멤버**
- 유틸리티 메소드 → **정적 메소드**

---

## 11. final 필드와 상수

### final 필드

**`final` 필드**는 초기값이 저장되면 **변경할 수 없는** 필드입니다.

**초기화 시점:**
1. 필드 선언 시
2. 생성자에서

```java
class Person {
    final String nation = "대한민국";  // 방법 1: 선언 시
    final String ssn;
    
    Person(String ssn) {
        this.ssn = ssn;  // 방법 2: 생성자에서
    }
}
```

```java
Person p = new Person("123456-1234567");
// p.nation = "미국";  // 컴파일 에러! final 필드 변경 불가
// p.ssn = "111111-1111111";  // 컴파일 에러!
```

---

### 상수 (Constant)

**상수**는 **불변의 값**을 저장하는 필드로, `static final`로 선언합니다.

**특징:**
- 객체마다 저장할 필요 없음 → `static`
- 한 번 초기화되면 변경 불가 → `final`
- 모두 **대문자**로 작성, 단어 구분은 **언더스코어(_)**

```java
class Earth {
    // 상수 선언
    static final double EARTH_RADIUS = 6400;
    static final double EARTH_SURFACE_AREA;
    
    // 정적 블록에서 초기화
    static {
        EARTH_SURFACE_AREA = 4 * Math.PI * EARTH_RADIUS * EARTH_RADIUS;
    }
}
```

**사용:**
```java
double radius = Earth.EARTH_RADIUS;
double area = Earth.EARTH_SURFACE_AREA;
```

### 일반 변수 vs final vs 상수

| 구분 | 선언 | 변경 가능 | 공유 |
|------|------|----------|------|
| **일반 필드** | `int x;` | ✅ | ❌ |
| **final 필드** | `final int x;` | ❌ | ❌ |
| **상수** | `static final int X;` | ❌ | ✅ |

---

## 12. 패키지 (Package)

### 패키지란?

**패키지**는 클래스를 **그룹화**하는 디렉토리입니다.

**목적:**
- 클래스를 **체계적으로 관리**
- 클래스명 충돌 방지
- **접근 제한** 기능 제공

### 패키지 선언

**문법:**
```java
package 상위패키지.하위패키지;
```

**규칙:**
- 소스 파일 **최상단**에 위치
- 패키지명은 **소문자** 사용
- 도메인 역순으로 작성 (관례)

**예시:**
```java
package com.company.project;

public class ClassName {
    // ...
}
```

**물리적 구조:**
```
src/
└── com/
    └── company/
        └── project/
            └── ClassName.java
```

---

### import 문

다른 패키지의 클래스를 사용하려면 **`import`** 문을 사용합니다.

```java
package com.mycompany;

import com.othercompany.Calculator;  // 단일 클래스 import
import java.util.*;                  // 패키지의 모든 클래스 import

public class Example {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
    }
}
```

**import가 필요 없는 경우:**
- **같은 패키지**의 클래스
- **java.lang 패키지**의 클래스 (String, System 등)

---

## 13. 접근 제한자 (Access Modifier)

### 접근 제한자란?

**접근 제한자**는 클래스, 생성자, 필드, 메소드의 **접근 범위를 제한**합니다.

**종류:**

| 접근 제한자 | 접근 범위 |
|------------|----------|
| `public` | 모든 패키지에서 접근 가능 |
| `protected` | 같은 패키지 + 상속받은 클래스 |
| `default` (생략) | 같은 패키지에서만 접근 가능 |
| `private` | 같은 클래스 내부에서만 접근 가능 |

### 접근 범위 비교

```
            같은 클래스  같은 패키지  다른 패키지(자식)  다른 패키지
public         ✅         ✅           ✅            ✅
protected      ✅         ✅           ✅            ❌
default        ✅         ✅           ❌            ❌
private        ✅         ❌           ❌            ❌
```

---

### 클래스의 접근 제한

**public 클래스:**
```java
package pack1;

public class A {  // 어디서나 사용 가능
}
```

**default 클래스:**
```java
package pack1;

class B {  // pack1 패키지 내에서만 사용 가능
}
```

---

### 생성자의 접근 제한

```java
public class A {
    // public 생성자 - 어디서나 객체 생성 가능
    public A(int x) { }
    
    // default 생성자 - 같은 패키지에서만 객체 생성
    A(String s) { }
    
    // private 생성자 - 외부에서 객체 생성 불가
    private A() { }
}
```

---

### 필드와 메소드의 접근 제한

```java
public class A {
    // public - 어디서나 접근 가능
    public int field1;
    public void method1() { }
    
    // default - 같은 패키지에서만 접근
    int field2;
    void method2() { }
    
    // private - 클래스 내부에서만 접근
    private int field3;
    private void method3() { }
}
```

---

## 14. Getter와 Setter

### Getter와 Setter란?

객체의 **무결성**을 지키기 위해 필드를 `private`으로 숨기고, **메소드를 통해** 접근하는 패턴입니다.

**Getter:** 필드값을 **읽는** 메소드  
**Setter:** 필드값을 **변경하는** 메소드

### Setter - 필드 값 검증

```java
public class Car {
    private int speed;
    
    // Setter - 유효성 검사 포함
    public void setSpeed(int speed) {
        if (speed < 0) {
            this.speed = 0;  // 음수 방지
        } else {
            this.speed = speed;
        }
    }
}
```

### Getter - 필드 값 변환

```java
public class Car {
    private int speed;
    
    // Getter - 단위 변환 예시
    public double getSpeedInMph() {
        return speed * 0.621371;  // km/h → mph
    }
}
```

---

### Getter/Setter 명명 규칙

```java
public class Person {
    private String name;
    private int age;
    private boolean married;
    
    // Getter
    public String getName() { return name; }
    public int getAge() { return age; }
    public boolean isMarried() { return married; }  // boolean은 is~
    
    // Setter
    public void setName(String name) { this.name = name; }
    public void setAge(int age) { this.age = age; }
    public void setMarried(boolean married) { this.married = married; }
}
```

**규칙:**
- Getter: `get필드명()` (boolean은 `is필드명()`)
- Setter: `set필드명(타입 매개변수)`
- 필드명 첫 글자는 **대문자**로 변환

---

### 완전한 예제

```java
public class Account {
    private String accountNo;
    private String owner;
    private int balance;
    
    // 생성자
    public Account(String accountNo, String owner) {
        this.accountNo = accountNo;
        this.owner = owner;
        this.balance = 0;
    }
    
    // Getter (읽기 전용)
    public String getAccountNo() {
        return accountNo;
    }
    
    public String getOwner() {
        return owner;
    }
    
    public int getBalance() {
        return balance;
    }
    
    // 입금 (유효성 검사)
    public void deposit(int amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    // 출금 (유효성 검사)
    public boolean withdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;
            return true;
        }
        return false;
    }
}
```

**사용:**
```java
Account account = new Account("123-456", "홍길동");
account.deposit(10000);
account.withdraw(3000);
System.out.println("잔액: " + account.getBalance());
```

---

## 15. 싱글톤 패턴 (Singleton Pattern)

### 싱글톤 패턴이란?

**싱글톤 패턴**은 클래스의 인스턴스를 **단 하나만** 생성하도록 보장하는 디자인 패턴입니다.

**사용 이유:**
- 공유 자원에 대한 동시 접근 제어
- 메모리 절약
- 전역 상태 관리

---

### 싱글톤 구현

**Singleton.java**
```java
public class Singleton {
    // 1. private static 필드에 자신의 인스턴스 저장
    private static Singleton instance = new Singleton();
    
    // 2. private 생성자 - 외부에서 객체 생성 불가
    private Singleton() {
    }
    
    // 3. public static 메소드 - 인스턴스 반환
    public static Singleton getInstance() {
        return instance;
    }
}
```

**사용:**
```java
public class SingletonExample {
    public static void main(String[] args) {
        // Singleton obj1 = new Singleton();  // 컴파일 에러!
        
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
        
        if (obj1 == obj2) {
            System.out.println("같은 Singleton 객체입니다.");
        } else {
            System.out.println("다른 Singleton 객체입니다.");
        }
    }
}
```

**실행 결과:**
```bash
같은 Singleton 객체입니다.
```

---

### 싱글톤 동작 원리

```
메소드 영역
┌─────────────────────────────┐
│ Singleton 클래스             │
│ - instance (static)   ──┐   │
└─────────────────────────┼───┘
                          │
힙 영역                    │
┌─────────────────────────┼───┐
│ Singleton 인스턴스 ←────┘    │
│ (단 하나만 존재)              │
└─────────────────────────────┘

obj1 ───→ Singleton 인스턴스
obj2 ───→ (같은 인스턴스)
```

---

### 지연 초기화 (Lazy Initialization)

필요할 때만 객체를 생성하는 방식입니다.

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {
    }
    
    public static Singleton getInstance() {
        if (instance == null) {  // 최초 호출 시에만 생성
            instance = new Singleton();
        }
        return instance;
    }
}
```
