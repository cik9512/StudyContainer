# C++ 객체지향 프로그래밍(OOP) 정리

## 1. 객체지향 4대 특징

| 특징 | 핵심 개념 |
|---|---|
| 캡슐화 | 데이터와 기능을 묶고 접근 통제 (`private`, `protected`, `public`) |
| 추상화 | 내부 구현을 숨기고 필요한 기능만 제공 (추상 클래스, 인터페이스) |
| 상속 | 부모 클래스의 특성을 물려받음 (IS-A 관계) |
| 다형성 | 동일한 인터페이스로 객체마다 다른 동작 수행 |

### 상속 vs 합성
- **상속(Inheritance):** IS-A 관계 (`Tank : Vehicle`)
- **합성(Composition):** HAS-A 관계 (`Tank` 내부에 `Engine` 객체)
- 일반적으로 상속보다 합성을 우선 고려 (결합도 감소, 유연성 증가)

---

## 2. 추상 클래스와 인터페이스

### C++
```cpp
class IDevice {
public:
    virtual void Start() = 0;
    virtual ~IDevice() = default;
};
```

- `virtual`: 자식 클래스에서 재정의 가능
- `virtual ... = 0`: 순수 가상함수
- 순수 가상함수가 하나라도 있으면 **추상 클래스**
- 순수 가상함수, 일반 함수, 멤버 변수 혼합 가능
- C++에는 별도 `interface` 키워드 없음
- 순수 가상함수 중심으로 구성한 추상 클래스를 인터페이스로 사용

### C#
```csharp
interface IDevice {
    void Start();
}
```

- `interface`: 인터페이스 정의
- `virtual`: 재정의 가능한 메서드
- `abstract`: 구현이 필요한 추상 메서드
- 일반적인 인터페이스 구현에 `virtual` 불필요

---

## 3. 다형성(Polymorphism)

| 종류 | 구현 방식 | 결정 시점 |
|---|---|---|
| 정적 다형성 | 오버로딩, 템플릿 | 컴파일타임 |
| 동적 다형성 | 가상함수, 오버라이딩 | 런타임 |

### 오버로딩 vs 오버라이딩
- **Overloading:** 함수 이름 동일, 매개변수 다름
- **Overriding:** 자식 클래스에서 부모의 가상함수 재정의
- `override`: 가상함수 재정의를 컴파일러가 검증

### virtual 동작
```cpp
class Parent {
public:
    virtual void Print() { cout << "Parent"; }
    virtual ~Parent() = default;
};

class Child : public Parent {
public:
    void Print() override { cout << "Child"; }
};

Child child;
Parent* ptr = &child;
ptr->Print(); // Child
```

- `virtual` 있음 → 실제 객체 타입 기준 호출
- `virtual` 없음 → 포인터/참조의 정적 타입 기준 호출
- `Child` 객체로 직접 호출하면 virtual 없이도 `Child::Print()` 호출 가능
- 내부적으로 일반적으로 `vptr`과 `vtable` 활용
- `= 0`: 순수 가상함수, 구체 클래스에서 구현 필요
- `final`: 추가 오버라이딩 금지

---

## 4. 상속 종류

- **단일 상속:** A → B
- **다중 상속:** A, B → C
- **다단계 상속:** A → B → C
- **계층적 상속:** A → B, C
- **혼합 상속:** 여러 상속 구조 결합
- **가상 상속:** 다이아몬드 상속의 기반 클래스 중복 방지

상속 접근 지정자: `public`, `protected`, `private`

---

## 5. 객체 슬라이싱 & 가상 소멸자

### 객체 슬라이싱
```cpp
Child child;
Parent p = child;   // 슬라이싱 발생
Parent& ref = child; // 슬라이싱 없음
Parent* ptr = &child; // 슬라이싱 없음
```

- 자식 객체를 부모 객체에 값 복사하면 자식 부분이 잘림
- 다형성 유지 시 포인터 또는 참조 사용

### 가상 소멸자
```cpp
Parent* p = new Child();
delete p;
```

- 기반 클래스 포인터로 파생 객체 삭제 시 일반적으로 `virtual` 소멸자 필요
- 없으면 정의되지 않은 동작이 발생할 수 있음

---

## 6. 템플릿(Template)

타입에 관계없이 코드 재사용 — **컴파일타임 다형성**

```cpp
template<typename T>
T Add(T a, T b) {
    return a + b;
}

Add<int>(1, 2);
Add<double>(1.5, 2.5);
```

- 함수 템플릿 / 클래스 템플릿
- `typename T`: 타입 매개변수
- `template<typename T, size_t N>`: 타입과 컴파일타임 값 전달
- `template<>`: 명시적 특수화
- C++20 `concept`: 템플릿 타입 제약
- C#의 Generic과 유사하지만 컴파일타임 기능이 더 다양함

---

## 7. 의존성 주입(DI) & 의존성 역전(DIP)

### DI — Dependency Injection
**필요한 객체를 클래스 내부에서 직접 생성하지 않고 외부에서 전달받는 것**

```cpp
class Simulator {
    ICommunication& comm;

public:
    Simulator(ICommunication& c) : comm(c) {}
};

UdpCommunication udp;
Simulator sim(udp); // 생성자 주입
```

- 생성자 주입 / Setter 주입 / 메서드 주입
- 생성자 `()`는 인자를 받는 구문, `: comm(c)`는 멤버 초기화
- 일반 설정값 전달은 보통 DI가 아님
- 인터페이스 없이 구체 객체를 전달해도 DI 가능
- 목적: 결합도 감소, 구현체 교체, 테스트 용이

### DIP — Dependency Inversion Principle
**구체 구현이 아닌 추상화에 의존하도록 설계하는 원칙**

```text
기존: Simulator → UdpCommunication

개선: Simulator → ICommunication ← UdpCommunication
```

- DI = 의존 객체를 외부에서 주입하는 구현 기법
- DIP = 추상화에 의존하도록 하는 설계 원칙
- 인터페이스만 사용한다고 DIP가 자동 적용되는 것은 아님

---

## 8. SOLID 원칙

| 원칙 | 핵심 |
|---|---|
| SRP | 단일 책임 — 클래스의 변경 이유를 하나로 |
| OCP | 개방-폐쇄 — 확장 가능, 기존 코드 수정 최소화 |
| LSP | 리스코프 치환 — 자식 객체가 부모의 동작 계약 유지 |
| ISP | 인터페이스 분리 — 불필요한 기능에 의존하지 않음 |
| DIP | 의존성 역전 — 구체 구현보다 추상화에 의존 |

### 핵심 암기

- `virtual` = 런타임 다형성
- `= 0` = 순수 가상함수
- `override` = 가상함수 재정의 검증
- `template` = 컴파일타임 타입 일반화
- 상속 = IS-A / 합성 = HAS-A
- DI = 외부에서 의존 객체 전달
- DIP = 구체 구현 대신 추상화에 의존
