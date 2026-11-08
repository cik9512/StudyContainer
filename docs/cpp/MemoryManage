
# C++ 메모리 관리 - RAII, Smart Pointer, GC

## 1. 개요

RAII, 스마트 포인터, GC는 모두 메모리 및 자원 관리와 관련된 개념이다.

| 구분 | RAII | Smart Pointer | GC |
|---|---|---|---|
| 주 사용 언어 | C++ | C++ | C#/.NET 등 |
| 개념 | 자원 관리 기법 | 자원 관리 클래스 | 자동 메모리 회수 시스템 |
| 관리 기준 | 객체의 수명 | 소유권 및 참조 카운트 | 객체의 도달 가능성 |
| 해제 시점 | 객체 소멸 시 | 소유권 종료 시 | GC 실행 시 |
| 관리 대상 | 메모리, 파일, 소켓 등 | 주로 동적 메모리 | 관리 힙 메모리 |

---

## 2. RAII (Resource Acquisition Is Initialization)

### 개념

RAII는 객체의 수명과 자원의 수명을 연결하는 C++ 자원 관리 기법이다.

- 객체 생성 시 자원 획득
- 객체 소멸 시 자원 해제
- 소멸자를 이용한 자동 자원 관리
- 예외 발생 시에도 자원 누수 방지에 유리

### RAII를 사용하지 않은 경우

```cpp
void Func()
{
    int* ptr = new int(10);

    // 작업 수행
    // 예외 발생 시 delete가 실행되지 않을 수 있음

    delete ptr;
}
```

문제점:
- `delete`를 직접 호출해야 한다.
- 예외나 조기 반환 시 메모리 누수가 발생할 수 있다.

### RAII 사용 예시

```cpp
#include <memory>

void Func()
{
    auto ptr = std::make_unique<int>(10);

    // 작업 수행
} // ptr 소멸 → 메모리 자동 해제
```

`unique_ptr`는 RAII를 구현하는 대표적인 클래스이다.

### 핵심

> RAII는 객체가 소멸할 때 소멸자를 통해 자원을 자동으로 해제하는 자원 관리 기법이다.

---

## 3. Smart Pointer (스마트 포인터)

### 개념

스마트 포인터는 동적 할당된 객체의 소유권과 수명을 관리하는 C++ 클래스이다.

대표적으로 세 가지가 있다.

- `unique_ptr`
- `shared_ptr`
- `weak_ptr`

### 3.1 unique_ptr

하나의 스마트 포인터가 객체를 단독으로 소유한다.

```cpp
#include <iostream>
#include <memory>

int main()
{
    auto ptr = std::make_unique<int>(10);

    std::cout << *ptr << '\n';

    return 0;
}
```

특징:
- 단독 소유권
- 복사 불가능
- 이동을 통한 소유권 이전 가능
- 소멸 시 객체 자동 해제

#### 소유권 이전

```cpp
auto p1 = std::make_unique<int>(10);

// auto p2 = p1; // 컴파일 오류

auto p2 = std::move(p1);

// p1은 더 이상 객체를 소유하지 않음
```

### 3.2 shared_ptr

여러 스마트 포인터가 동일 객체를 공동으로 소유한다.

```cpp
#include <iostream>
#include <memory>

int main()
{
    auto p1 = std::make_shared<int>(10);

    auto p2 = p1;

    std::cout << p1.use_count() << '\n'; // 2
}
```

특징:
- 공동 소유권
- 참조 카운트 사용
- 마지막 소유자가 사라지면 객체 해제
- 참조 카운트 관리에 추가 비용 발생

### 3.3 weak_ptr

`shared_ptr`가 관리하는 객체를 소유권 없이 참조한다.

```cpp
auto shared = std::make_shared<int>(10);

std::weak_ptr<int> weak = shared;

if (auto ptr = weak.lock())
{
    std::cout << *ptr << '\n';
}
```

특징:
- 소유권 없음
- 강한 참조 카운트를 증가시키지 않음
- `lock()`으로 객체가 살아 있는지 확인
- shared_ptr 간 순환 참조 방지에 활용

### Smart Pointer 비교

| 구분 | unique_ptr | shared_ptr | weak_ptr |
|---|---|---|---|
| 소유권 | 단독 | 공동 | 없음 |
| 복사 | 불가능 | 가능 | 가능 |
| 참조 카운트 | 없음 | 있음 | 소유 카운트 증가 없음 |
| 객체 수명 연장 | 가능 | 가능 | 불가능 |
| 주요 목적 | 단독 소유 | 공동 소유 | 비소유 참조 |

### 핵심

> 기본적으로 단독 소유가 가능하면 unique_ptr를 사용하고, 공동 소유가 필요한 경우 shared_ptr를 사용한다.

---

## 4. GC (Garbage Collection)

### 개념

GC는 프로그램에서 더 이상 접근할 수 없는 관리 객체의 메모리를 자동으로 회수하는 기술이다.

C#/.NET에서 대표적으로 사용한다.

### C# 예시

```csharp
class Data
{
    public int Value { get; set; }
}

void Func()
{
    Data data = new Data();
    data.Value = 10;
}
```

`Func()` 실행 이후 객체에 더 이상 접근할 수 없다면 GC의 회수 대상이 될 수 있다.

단, 함수가 종료되었다고 즉시 메모리가 해제되는 것은 아니다.

### GC 동작 개념

1. 관리 힙에 객체 할당
2. GC가 루트에서 접근 가능한 객체 추적
3. 도달할 수 없는 객체 식별
4. 해당 객체의 메모리 회수

.NET GC는 세대(Generation) 기반 관리 방식도 사용한다.

- Generation 0: 새로 생성된 객체
- Generation 1: 중간 수명의 객체
- Generation 2: 오래 살아남은 객체

### GC의 주의점

GC는 관리 메모리를 자동 회수하지만, 파일이나 소켓 같은 자원의 즉각적인 해제까지 보장하지 않는다.

이러한 자원은 `IDisposable`과 `using` 등을 이용해 관리한다.

```csharp
using var stream = new FileStream(
    "test.bin",
    FileMode.Open
);

// 파일 읽기
// 범위 종료 시 Dispose 호출
```

### 핵심

> GC는 더 이상 접근할 수 없는 관리 객체의 메모리를 자동으로 회수하지만, 회수 시점은 개발자가 직접 결정하지 않는다.

---

## 5. RAII와 GC의 차이

### RAII

```cpp
void Func()
{
    auto ptr = std::make_unique<int>(10);
} // 객체 소멸 시 메모리 해제
```

- 객체의 수명을 기반으로 자원 관리
- 일반적인 지역 객체는 범위 종료 시 소멸
- 자원의 해제 시점을 예측하기 쉬움

### GC

```csharp
void Func()
{
    var data = new Data();
} // 즉시 해제되는 것은 아님
```

- 객체의 도달 가능성을 기반으로 관리 메모리 회수
- GC가 적절한 시점에 실행
- 개발자가 직접 메모리 해제를 수행할 필요가 적음

### 차이점 정리

**RAII**
- 객체 수명과 자원 수명을 연결한다.
- 소멸자를 통해 자원을 해제한다.
- 메모리뿐 아니라 파일, Mutex 등도 관리한다.

**GC**
- 더 이상 도달할 수 없는 관리 객체를 회수한다.
- 메모리 회수 시점이 비결정적이다.
- 비관리 자원은 별도로 관리해야 한다.

---

## 6. 기술면접 예상 질문

### Q1. RAII란 무엇인가요?

RAII는 객체의 수명과 자원의 수명을 연결하는 C++ 자원 관리 기법입니다.

객체 생성 시 자원을 확보하고 소멸 시 자동으로 해제하도록 하여 메모리 누수와 자원 누수를 방지합니다.

### Q2. 스마트 포인터란 무엇인가요?

스마트 포인터는 RAII를 기반으로 동적 메모리의 수명을 자동으로 관리하는 C++ 클래스입니다.

`unique_ptr`는 단독 소유, `shared_ptr`는 공동 소유를 지원하며, `weak_ptr`는 소유권 없이 객체를 참조할 때 사용합니다.

### Q3. unique_ptr와 shared_ptr의 차이는 무엇인가요?

`unique_ptr`는 하나의 객체를 단독으로 소유하고, 복사가 불가능하며 이동만 가능합니다.

`shared_ptr`는 여러 포인터가 동일 객체를 공동 소유하며 참조 카운트가 0이 되면 객체를 해제합니다.

### Q4. weak_ptr는 왜 사용하나요?

`shared_ptr` 간 순환 참조로 인해 객체가 해제되지 않는 문제를 방지하기 위해 사용합니다.

`weak_ptr`는 객체의 소유권을 가지지 않으며, `lock()`으로 객체가 살아 있는지 확인할 수 있습니다.

### Q5. GC란 무엇인가요?

GC는 더 이상 접근할 수 없는 객체의 메모리를 자동으로 회수하는 기술입니다.

C#에서는 .NET GC가 관리 힙의 메모리를 회수하지만, 파일이나 소켓 등은 `IDisposable`과 `using`을 이용해 별도로 관리해야 합니다.

### Q6. RAII와 GC의 차이는 무엇인가요?

RAII는 객체의 수명에 따라 소멸자를 통해 자원을 해제하는 방식입니다.

GC는 객체의 도달 가능성을 확인하여 더 이상 사용하지 않는 관리 객체의 메모리를 자동으로 회수하는 방식입니다.

---

## 7. 최종 요약

| 개념 | 한 줄 정리 |
|---|---|
| RAII | 객체의 수명과 자원 수명을 연결하는 자원 관리 기법 |
| unique_ptr | 단독 소유권을 가진 스마트 포인터 |
| shared_ptr | 참조 카운트 기반 공동 소유 스마트 포인터 |
| weak_ptr | 객체를 소유하지 않는 약한 참조 |
| GC | 도달할 수 없는 관리 객체의 메모리를 자동 회수하는 시스템 |

**핵심 암기**

- RAII = 객체 수명 기반 자원 관리
- Smart Pointer = RAII를 활용한 C++ 자원 관리 도구
- GC = 객체 도달 가능성 기반 자동 메모리 회수
