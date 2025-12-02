# Java 기초 - 상속(Inheritance)

## 1. 상속 개념

### 상속이란?

**상속(Inheritance)**은 부모 클래스의 필드와 메소드를 자식 클래스가 물려받는 것입니다.

**비유:**
```
부모: 자동차 (공통 기능)
  ↓ 상속
자식: 스포츠카, SUV, 트럭 (특화 기능 추가)
```

### 상속의 장점

| 장점 | 설명 |
|------|------|
| **코드 재사용** | 이미 작성된 코드를 다시 작성하지 않음 |
| **중복 제거** | 공통 코드를 부모 클래스에 한 번만 작성 |
| **유지보수 용이** | 수정이 필요하면 부모 클래스만 수정 |
| **다형성 구현** | 부모 타입으로 다양한 자식 객체 처리 |

### 상속 관계 용어

```
부모 클래스 (Parent Class)
  = 상위 클래스 (Super Class)
  = 기반 클래스 (Base Class)
    ↓ extends
자식 클래스 (Child Class)
  = 하위 클래스 (Sub Class)
  = 파생 클래스 (Derived Class)
```

---

## 2. 클래스 상속

### 상속 선언

**문법:**
```java
public class 자식클래스 extends 부모클래스 {
    // 추가 필드
    // 추가 메소드
}
```

> **중요:** Java는 **단일 상속**만 지원합니다. (다중 상속 불가)

**Phone.java** (부모 클래스)
```java
package ch07.sec02;

public class Phone {
    public String model;
    public String color;
    
    public void bell() {
        System.out.println("벨이 울립니다.");
    }
    
    public void sendVoice(String message) {
        System.out.println("자기: " + message);
    }
    
    public void receiveVoice(String message) {
        System.out.println("상대방: " + message);
    }
    
    public void hangUp() {
        System.out.println("전화를 끊습니다.");
    }
}
```

**SmartPhone.java** (자식 클래스)
```java
package ch07.sec02;

public class SmartPhone extends Phone {
    public boolean wifi;
    
    public SmartPhone(String model, String color) {
        // 부모로부터 상속받은 필드 사용
        this.model = model;
        this.color = color;
    }
    
    public void setWifi(boolean wifi) {
        this.wifi = wifi;
        System.out.println("와이파이 상태를 변경했습니다.");
    }
    
    public void internet() {
        System.out.println("인터넷에 연결합니다.");
    }
}
```

**SmartPhoneExample.java**
```java
package ch07.sec02;

public class SmartPhoneExample {
    public static void main(String[] args) {
        SmartPhone myPhone = new SmartPhone("갤럭시", "은색");
        
        // 부모로부터 상속받은 필드
        System.out.println("모델: " + myPhone.model);
        System.out.println("색상: " + myPhone.color);
        
        // 자식 클래스의 필드
        System.out.println("와이파이 상태: " + myPhone.wifi);
        
        // 부모로부터 상속받은 메소드
        myPhone.bell();
        myPhone.sendVoice("여보세요");
        myPhone.receiveVoice("안녕하세요");
        myPhone.sendVoice("반갑습니다.");
        myPhone.hangUp();
        
        // 자식 클래스의 메소드
        myPhone.setWifi(true);
        myPhone.internet();
    }
}
```

**실행 결과:**
```bash
모델: 갤럭시
색상: 은색
와이파이 상태: false
벨이 울립니다.
자기: 여보세요
상대방: 안녕하세요
자기: 반갑습니다.
전화를 끊습니다.
와이파이 상태를 변경했습니다.
인터넷에 연결합니다.
```

---

### 상속 관계 다이어그램

```
┌─────────────────────┐
│      Phone          │ (부모)
├─────────────────────┤
│ + model: String     │
│ + color: String     │
├─────────────────────┤
│ + bell()            │
│ + sendVoice()       │
│ + receiveVoice()    │
│ + hangUp()          │
└─────────────────────┘
          △ (상속)
          │
┌─────────────────────┐
│    SmartPhone       │ (자식)
├─────────────────────┤
│ + wifi: boolean     │ (추가)
├─────────────────────┤
│ + setWifi()         │ (추가)
│ + internet()        │ (추가)
└─────────────────────┘
```

---

## 3. 부모 생성자 호출

### 객체 생성 순서

자식 객체를 생성하면 **부모 객체가 먼저 생성**된 후 자식 객체가 생성됩니다.

```
1. 자식 생성자 호출
2. 부모 생성자 호출 (super())
3. 부모 객체 생성
4. 부모 생성자 실행
5. 자식 생성자 실행
6. 자식 객체 생성 완료
```

### super() 키워드

**`super()`**는 부모 클래스의 생성자를 호출합니다.

**규칙:**
- 자식 생성자의 **첫 줄**에 위치해야 함
- 생략하면 **`super()`**(기본 생성자)가 자동 추가됨
- 명시적으로 작성하면 원하는 부모 생성자 호출 가능

---

### 예제 1: 부모 클래스에 기본 생성자가 있는 경우

**Phone.java**
```java
package ch07.sec03.exam01;

public class Phone {
    public String model;
    public String color;
    
    // 기본 생성자
    public Phone() {
        System.out.println("Phone() 생성자 실행");
    }
}
```

**SmartPhone.java**
```java
package ch07.sec03.exam01;

public class SmartPhone extends Phone {
    public SmartPhone(String model, String color) {
        super();  // 생략 가능 (자동 추가됨)
        this.model = model;
        this.color = color;
        System.out.println("SmartPhone(String model, String color) 생성자 실행");
    }
}
```

**SmartPhoneExample.java**
```java
package ch07.sec03.exam01;

public class SmartPhoneExample {
    public static void main(String[] args) {
        SmartPhone myPhone = new SmartPhone("갤럭시", "은색");
        
        System.out.println("모델: " + myPhone.model);
        System.out.println("색상: " + myPhone.color);
    }
}
```

**실행 결과:**
```bash
Phone() 생성자 실행
SmartPhone(String model, String color) 생성자 실행
모델: 갤럭시
색상: 은색
```

---

### 예제 2: 부모 클래스에 매개변수 생성자만 있는 경우

**Phone.java**
```java
package ch07.sec03.exam02;

public class Phone {
    public String model;
    public String color;
    
    // 매개변수 생성자 (기본 생성자 없음!)
    public Phone(String model, String color) {
        this.model = model;
        this.color = color;
        System.out.println("Phone(String model, String color) 생성자 실행");
    }
}
```

**SmartPhone.java**
```java
package ch07.sec03.exam02;

public class SmartPhone extends Phone {
    public SmartPhone(String model, String color) {
        super(model, color);  // 반드시 명시적 호출 필요!
        System.out.println("SmartPhone(String model, String color) 생성자 실행");
    }
}
```

**SmartPhoneExample.java**
```java
package ch07.sec03.exam02;

public class SmartPhoneExample {
    public static void main(String[] args) {
        SmartPhone myPhone = new SmartPhone("갤럭시", "은색");

        System.out.println("모델: " + myPhone.model);
        System.out.println("색상: " + myPhone.color);
    }
}
```

**실행 결과:**
```bash
Phone(String model, String color) 생성자 실행
SmartPhone(String model, String color) 생성자 실행
모델: 갤럭시
색상: 은색
```

> **주의:** 부모 클래스에 기본 생성자가 없고 매개변수 생성자만 있으면, 자식 생성자에서 반드시 `super(매개값, ...)`를 명시적으로 호출해야 합니다!

---

### super vs this

| 키워드 | 의미 | 사용 예 |
|--------|------|---------|
| `super()` | 부모 생성자 호출 | `super(model, color);` |
| `super.필드` | 부모 필드 접근 | `super.model = "A";` |
| `super.메소드()` | 부모 메소드 호출 | `super.run();` |
| `this()` | 같은 클래스의 다른 생성자 호출 | `this(model);` |
| `this.필드` | 현재 객체의 필드 | `this.model = model;` |
| `this.메소드()` | 현재 객체의 메소드 | `this.run();` |

---

## 4. 메소드 재정의 (Override)

### 메소드 오버라이딩이란?

**메소드 오버라이딩(Method Overriding)**은 부모 클래스의 메소드를 자식 클래스에서 **재정의**하는 것입니다.

**목적:**
- 부모 메소드가 자식 클래스에 맞지 않을 때
- 자식 클래스에 특화된 동작 구현

### 오버라이딩 규칙

1. **메소드 시그니처 동일**: 메소드명, 매개변수 타입/개수/순서 같아야 함
2. **리턴 타입 동일** (또는 자식 타입)
3. **접근 제한자**: 같거나 더 넓게 (좁게 불가)
4. **예외**: 부모보다 많이 throws 불가

```java
// 부모 클래스
public class Parent {
    public void method() { }
}

// 자식 클래스
public class Child extends Parent {
    // ✅ 올바른 오버라이딩
    @Override
    public void method() { }  // public → public (같음)
    
    // ❌ 잘못된 오버라이딩
    // private void method() { }  // public → private (더 좁음)
}
```

---

### @Override 어노테이션

**`@Override`**는 메소드가 정확히 오버라이딩되었는지 **컴파일러가 체크**하게 합니다.

```java
@Override
public void method() {
    // 오버라이딩 코드
}
```

**장점:**
- 오타나 실수 방지
- 코드 가독성 향상
- 리팩토링 시 안전성

---

### 오버라이딩 예제

**Calculator.java**
```java
package ch07.sec04.exam01;

public class Calculator {
    public double areaCircle(double r) {
        System.out.println("Calculator 객체의 areaCircle() 실행");
        return 3.14159 * r * r;
    }
}
```

**Computer.java**
```java
package ch07.sec04.exam01;

public class Computer extends Calculator {
    @Override
    public double areaCircle(double r) {
        System.out.println("Computer 객체의 areaCircle() 실행");
        return Math.PI * r * r;  // 더 정확한 값 사용
    }
}
```

**ComputerExample.java**
```java
package ch07.sec04.exam01;

public class ComputerExample {
    public static void main(String[] args) {
        int r = 10;
        
        Calculator calculator = new Calculator();
        System.out.println("원 면적: " + calculator.areaCircle(r));
        System.out.println();
        
        Computer computer = new Computer();
        System.out.println("원 면적: " + computer.areaCircle(r));
    }
}
```

**실행 결과:**
```bash
Calculator 객체의 areaCircle() 실행
원 면적: 314.159

Computer 객체의 areaCircle() 실행
원 면적: 314.1592653589793
```

---

### 부모 메소드 호출 (super.메소드())

오버라이딩된 부모 메소드를 호출하려면 **`super.메소드명()`**을 사용합니다.

**Airplane.java**
```java
package ch07.sec04.exam02;

public class Airplane {
    public void land() {
        System.out.println("착륙합니다.");
    }
    
    public void fly() {
        System.out.println("일반 비행합니다.");
    }
    
    public void takeOff() {
        System.out.println("이륙합니다.");
    }
}
```

**SupersonicAirplane.java**
```java
package ch07.sec04.exam02;

public class SupersonicAirplane extends Airplane {
    public static final int NORMAL = 1;
    public static final int SUPERSONIC = 2;
    
    public int flyMode = NORMAL;
    
    @Override
    public void fly() {
        if (flyMode == SUPERSONIC) {
            System.out.println("초음속 비행합니다.");
        } else {
            super.fly();  // 부모의 fly() 메소드 호출
        }
    }
}
```

**SupersonicAirplaneExample.java**
```java
package ch07.sec04.exam02;

public class SupersonicAirplaneExample {
    public static void main(String[] args) {
        SupersonicAirplane sa = new SupersonicAirplane();
        
        sa.takeOff();
        sa.fly();
        sa.flyMode = SupersonicAirplane.SUPERSONIC;
        sa.fly();
        sa.flyMode = SupersonicAirplane.NORMAL;
        sa.fly();
        sa.land();
    }
}
```

**실행 결과:**
```bash
이륙합니다.
일반 비행합니다.
초음속 비행합니다.
일반 비행합니다.
착륙합니다.
```

---

### 오버로딩 vs 오버라이딩

| 구분 | 오버로딩 (Overloading) | 오버라이딩 (Overriding) |
|------|----------------------|------------------------|
| **의미** | 같은 이름, 다른 매개변수 | 부모 메소드 재정의 |
| **관계** | 같은 클래스 내 | 상속 관계 |
| **메소드명** | 동일 | 동일 |
| **매개변수** | **다름** | 동일 |
| **리턴 타입** | 무관 | 동일 (또는 자식 타입) |

```java
// 오버로딩
class Calculator {
    int plus(int x, int y) { return x + y; }
    double plus(double x, double y) { return x + y; }  // 매개변수 다름
}

// 오버라이딩
class Parent {
    void method() { }
}
class Child extends Parent {
    @Override
    void method() { }  // 재정의
}
```

---

## 5. final 클래스와 final 메소드

### final 클래스

**`final class`**는 더 이상 **상속할 수 없는** 클래스입니다.

```java
public final class String {
    // String 클래스는 상속 불가
}

// public class MyString extends String { }  // 컴파일 에러!
```

**사용 이유:**
- 보안상 중요한 클래스 보호
- 성능 최적화
- 설계의 안정성

**예시:** `String`, `Math`, `System` 등

---

### final 메소드

**`final method`**는 자식 클래스에서 **오버라이딩할 수 없는** 메소드입니다.

```java
public class Car {
    // 오버라이딩 가능
    public void run() {
        System.out.println("달립니다.");
    }
    
    // 오버라이딩 불가
    public final void stop() {
        System.out.println("멈춥니다.");
    }
}

public class SportsCar extends Car {
    @Override
    public void run() {
        System.out.println("빠르게 달립니다.");
    }
    
    // @Override
    // public void stop() { }  // 컴파일 에러! final 메소드 오버라이딩 불가
}
```

---

## 6. protected 접근 제한자

### protected란?

**`protected`**는 **같은 패키지** 또는 **다른 패키지의 자식 클래스**에서 접근 가능합니다.

### 접근 범위 비교

```
            같은 클래스  같은 패키지  다른 패키지(자식)  다른 패키지
public         ✅         ✅           ✅            ✅
protected      ✅         ✅           ✅            ❌
default        ✅         ✅           ❌            ❌
private        ✅         ❌           ❌            ❌
```

### protected 예제

**package1/A.java**
```java
package package1;

public class A {
    protected String field;
    
    protected void method() {
        System.out.println("A 클래스의 method");
    }
}
```

**package1/B.java** (같은 패키지)
```java
package package1;

public class B {
    public void method() {
        A a = new A();
        a.field = "value";  // ✅ 같은 패키지에서 접근 가능
        a.method();
    }
}
```

**package2/C.java** (다른 패키지, 자식 클래스)
```java
package package2;

import package1.A;

public class C extends A {
    public void method() {
        this.field = "value";  // ✅ 상속받았으므로 접근 가능
        this.method();
        
        A a = new A();
        // a.field = "value";  // ❌ 다른 패키지, 상속 관계 아니면 불가
    }
}
```

---

## 7. 타입 변환 (Type Casting)

### 자동 타입 변환 (Promotion)

**자동 타입 변환**은 자식 객체를 부모 타입으로 자동 변환하는 것입니다.

**문법:**
```java
부모타입 변수 = 자식객체;
```

**예시:**
```java
class Animal { }
class Cat extends Animal { }

Animal animal = new Cat();  // 자동 타입 변환
```

### 자동 타입 변환 후 특징

```java
class Parent {
    void method1() { }
    void method2() { }
}

class Child extends Parent {
    @Override
    void method2() { }  // 오버라이딩
    void method3() { }  // 자식만의 메소드
}

Parent parent = new Child();  // 자동 타입 변환

parent.method1();  // ✅ 부모 메소드 호출 가능
parent.method2();  // ✅ 자식의 오버라이딩된 메소드 실행!
// parent.method3();  // ❌ 자식만의 메소드는 호출 불가
```

> **핵심:** 
> - **사용 가능:** 부모 타입에 선언된 필드/메소드
> - **실제 실행:** 자식 객체의 오버라이딩된 메소드

---

### 강제 타입 변환 (Casting)

**강제 타입 변환**은 부모 타입을 자식 타입으로 변환하는 것입니다.

**문법:**
```java
자식타입 변수 = (자식타입) 부모타입;
```

**조건:** 원래 자식 객체였던 것만 가능!

```java
// ✅ 올바른 강제 타입 변환
Parent parent = new Child();  // 자동 타입 변환
Child child = (Child) parent;  // 강제 타입 변환 성공

// ❌ 잘못된 강제 타입 변환
Parent parent = new Parent();
Child child = (Child) parent;  // ClassCastException 발생!
```

---

### instanceof 연산자

**`instanceof`**는 객체가 특정 클래스의 인스턴스인지 확인합니다.

**문법:**
```java
boolean result = 객체 instanceof 타입;
```

**안전한 강제 타입 변환:**
```java
public void method(Parent parent) {
    if (parent instanceof Child) {
        Child child = (Child) parent;  // 안전한 캐스팅
        child.method3();
    }
}
```

**Java 16+ 패턴 매칭:**
```java
public void method(Parent parent) {
    if (parent instanceof Child child) {  // 자동으로 캐스팅
        child.method3();  // 바로 사용 가능
    }
}
```

---

## 8. 다형성 (Polymorphism)

### 다형성이란?

**다형성**은 같은 타입이지만 **실행 결과가 다양**하게 나오는 성질입니다.

**구현 방법:**
1. 자동 타입 변환
2. 메소드 오버라이딩

### 필드 다형성

**Tire.java**
```java
package ch07.sec08.exam01;

public class Tire {
    public void roll() {
        System.out.println("회전합니다.");
    }
}
```

**HankookTire.java**
```java
package ch07.sec08.exam01;

public class HankookTire extends Tire {
    @Override
    public void roll() {
        System.out.println("한국 타이어가 회전합니다.");
    }
}
```

**KumhoTire.java**
```java
package ch07.sec08.exam01;

public class KumhoTire extends Tire {
    @Override
    public void roll() {
        System.out.println("금호 타이어가 회전합니다.");
    }
}
```

**Car.java**
```java
package ch07.sec08.exam01;

public class Car {
    public Tire tire;  // 부모 타입 필드
    
    public void run() {
        tire.roll();  // 다형성 발생!
    }
}
```

**CarExample.java**
```java
package ch07.sec08.exam01;

public class CarExample {
    public static void main(String[] args) {
        Car myCar = new Car();
        
        myCar.tire = new Tire();
        myCar.run();
        
        myCar.tire = new HankookTire();  // 교체
        myCar.run();
        
        myCar.tire = new KumhoTire();  // 교체
        myCar.run();
    }
}
```

**실행 결과:**
```bash
회전합니다.
한국 타이어가 회전합니다.
금호 타이어가 회전합니다.
```

---

### 매개변수 다형성

**Vehicle.java**
```java
package ch07.sec08.exam02;

public class Vehicle {
    public void run() {
        System.out.println("차량이 달립니다.");
    }
}
```

**Bus.java**
```java
package ch07.sec08.exam02;

public class Bus extends Vehicle {
    @Override
    public void run() {
        System.out.println("버스가 달립니다.");
    }
}
```

**Taxi.java**
```java
package ch07.sec08.exam02;

public class Taxi extends Vehicle {
    @Override
    public void run() {
        System.out.println("택시가 달립니다.");
    }
}
```

**Driver.java**
```java
package ch07.sec08.exam02;

public class Driver {
    // 부모 타입 매개변수로 모든 자식 객체 처리
    public void drive(Vehicle vehicle) {
        vehicle.run();  // 다형성!
    }
}
```

**DriverExample.java**
```java
package ch07.sec08.exam02;

public class DriverExample {
    public static void main(String[] args) {
        Driver driver = new Driver();
        
        Bus bus = new Bus();
        driver.drive(bus);  // Bus 객체 전달
        
        Taxi taxi = new Taxi();
        driver.drive(taxi);  // Taxi 객체 전달
    }
}
```

**실행 결과:**
```bash
버스가 달립니다.
택시가 달립니다.
```

### 다형성의 장점

```java
// 다형성 없이 (나쁜 예)
class Driver {
    void driveBus(Bus bus) { bus.run(); }
    void driveTaxi(Taxi taxi) { taxi.run(); }
    void driveTruck(Truck truck) { truck.run(); }
    // 차량이 추가될 때마다 메소드 추가 필요
}

// 다형성 사용 (좋은 예)
class Driver {
    void drive(Vehicle vehicle) {
        vehicle.run();  // 모든 차량 처리!
    }
}
```

---

## 9. 추상 클래스 (Abstract Class)

### 추상 클래스란?

**추상 클래스**는 **공통 특성을 추출**한 클래스로, **직접 객체 생성은 불가능**합니다.

**목적:**
- 실체 클래스의 **공통 부모** 역할
- **설계 표준** 제공
- 코드 재사용

### 추상 클래스 선언

**문법:**
```java
public abstract class 클래스명 {
    // 필드
    // 생성자
    // 메소드
    // 추상 메소드
}
```

**Phone.java**
```java
package ch07.sec10.exam01;

public abstract class Phone {
    String owner;
    
    Phone(String owner) {
        this.owner = owner;
    }
    
    void turnOn() {
        System.out.println("폰 전원을 켭니다.");
    }
    
    void turnOff() {
        System.out.println("폰 전원을 끕니다.");
    }
}
```

**SmartPhone.java**
```java
package ch07.sec10.exam01;

public class SmartPhone extends Phone {
    SmartPhone(String owner) {
        super(owner);
    }
    
    void internetSearch() {
        System.out.println("인터넷 검색을 합니다.");
    }
}
```

**PhoneExample.java**
```java
package ch07.sec10.exam01;

public class PhoneExample {
    public static void main(String[] args) {
        // Phone phone = new Phone("홍길동");  // 컴파일 에러! 추상 클래스는 객체 생성 불가
        
        SmartPhone smartPhone = new SmartPhone("홍길동");
        smartPhone.turnOn();
        smartPhone.internetSearch();
        smartPhone.turnOff();
    }
}
```

**실행 결과:**
```bash
폰 전원을 켭니다.
인터넷 검색을 합니다.
폰 전원을 끕니다.
```

---

### 추상 메소드

**추상 메소드**는 **선언부만 있고 실행 블록이 없는** 메소드입니다.

**문법:**
```java
public abstract 리턴타입 메소드명(매개변수, ...);
```

**특징:**
- 자식 클래스에서 **반드시 오버라이딩** 해야 함
- 설계 표준 제공

**Animal.java**
```java
package ch07.sec10.exam02;

public abstract class Animal {
    // 일반 메소드
    public void breathe() {
        System.out.println("숨을 쉽니다.");
    }
    
    // 추상 메소드
    public abstract void sound();
}
```

**Dog.java**
```java
package ch07.sec10.exam02;

public class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("멍멍");
    }
}
```

**Cat.java**
```java
package ch07.sec10.exam02;

public class Cat extends Animal {
    @Override
    public void sound() {
        System.out.println("야옹");
    }
}
```

**AbstractMethodExample.java**
```java
package ch07.sec10.exam02;

public class AbstractMethodExample {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.sound();
        
        Cat cat = new Cat();
        cat.sound();
        
        // 다형성 활용
        animalSound(new Dog());
        animalSound(new Cat());
    }
    
    public static void animalSound(Animal animal) {
        animal.sound();  // 다형성!
    }
}
```

**실행 결과:**
```bash
멍멍
야옹
멍멍
야옹
```

---

### 추상 클래스 vs 일반 클래스

| 구분 | 일반 클래스 | 추상 클래스 |
|------|-----------|-----------|
| **객체 생성** | 가능 | 불가능 |
| **추상 메소드** | 불가능 | 가능 |
| **일반 메소드** | 가능 | 가능 |
| **목적** | 객체 생성용 | 상속용 (설계 표준) |

### 템플릿 메소드 패턴

추상 클래스의 대표적인 활용 패턴입니다.

```java
public abstract class GameCharacter {
    // 템플릿 메소드 (변하지 않는 부분)
    public final void play() {
        run();
        attack();
        specialSkill();  // 캐릭터마다 다름
        defend();
    }
    
    private void run() {
        System.out.println("달립니다.");
    }
    
    private void attack() {
        System.out.println("공격합니다.");
    }
    
    // 추상 메소드 (변하는 부분)
    public abstract void specialSkill();
    
    private void defend() {
        System.out.println("방어합니다.");
    }
}

class Warrior extends GameCharacter {
    @Override
    public void specialSkill() {
        System.out.println("검 휘두르기!");
    }
}

class Wizard extends GameCharacter {
    @Override
    public void specialSkill() {
        System.out.println("마법 공격!");
    }
}
```

---

## 10. 봉인된 클래스 (Sealed Class, Java 15+)

### 봉인된 클래스란?

**봉인된 클래스**는 **허용된 자식 클래스만** 상속할 수 있는 클래스입니다. (Java 15+)

**목적:**
- 무분별한 상속 방지
- 클래스 계층 구조 명확화
- 타입 안전성 향상

### 봉인 클래스 선언

**문법:**
```java
public sealed class 클래스명 permits 자식1, 자식2, ... {
}
```

**Person.java**
```java
package ch07.sec11;

public sealed class Person permits Employee, Manager {
    public String name;
    
    public void work() {
        System.out.println("하는 일이 결정되지 않았습니다.");
    }
}
```

---

### 봉인된 클래스의 자식 클래스

봉인된 클래스를 상속받은 자식 클래스는 다음 중 하나를 선택해야 합니다:

| 키워드 | 의미 |
|--------|------|
| `final` | 더 이상 상속 불가 |
| `sealed` | 다시 봉인 (permits로 자식 지정) |
| `non-sealed` | 봉인 해제 (자유롭게 상속 가능) |

**Employee.java** (final)
```java
package ch07.sec11;

public final class Employee extends Person {
    @Override
    public void work() {
        System.out.println("제품을 생산합니다.");
    }
}

// class SubEmployee extends Employee { }  // 컴파일 에러! final 클래스 상속 불가
```

**Manager.java** (non-sealed)
```java
package ch07.sec11;

public non-sealed class Manager extends Person {
    @Override
    public void work() {
        System.out.println("생산 관리를 합니다.");
    }
}
```

**Director.java** (Manager를 상속 가능)
```java
package ch07.sec11;

public class Director extends Manager {
    @Override
    public void work() {
        System.out.println("제품을 기획합니다.");
    }
}
```

**SealedExample.java**
```java
package ch07.sec11;

public class SealedExample {
    public static void main(String[] args) {
        Person p = new Person();  // Person이 추상 클래스가 아니면 가능
        Employee e = new Employee();
        Manager m = new Manager();
        Director d = new Director();
        
        p.work();
        e.work();
        m.work();
        d.work();
    }
}
```

**실행 결과:**
```bash
하는 일이 결정되지 않았습니다.
제품을 생산합니다.
생산 관리를 합니다.
제품을 기획합니다.
```

---

### 봉인 클래스 계층 구조

```
┌──────────────────┐
│ Person (sealed)  │ ← permits Employee, Manager만
└─────┬────────────┘
      │
      ├─→ Employee (final) ← 더 이상 상속 불가
      │
      └─→ Manager (non-sealed) ← 자유롭게 상속 가능
             └─→ Director
```
