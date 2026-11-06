# C++ Lambda (람다 표현식)

## 1. 람다(Lambda) 개념

람다 표현식(Lambda Expression)은 C++11부터 도입된 **이름 없는 함수 객체(Anonymous Function Object)** 이다.

별도의 함수를 선언하지 않고 코드 내부에서 필요한 동작을 정의하고, 해당 동작을 다른 함수에 전달하거나 나중에 실행할 수 있도록 저장할 수 있다.

컴파일러는 람다 표현식마다 고유한 클로저 타입(Closure Type)을 생성하며, 내부적으로 함수 호출 연산자인 `operator()`를 가진 객체로 처리한다.

### 1.1. 람다를 사용하는 이유

- 다른 함수에 실행할 동작을 전달하기 위해
- 간단한 일회성 로직을 별도 함수 선언 없이 구현하기 위해
- 외부 지역변수를 캡처하여 상태를 보관하기 위해
- STL 알고리즘에 비교 함수나 조건식을 전달하기 위해
- 콜백 함수 및 이벤트 처리에 사용하기 위해
- 비동기 및 멀티스레드 작업을 정의하기 위해

**람다의 핵심 목적은 코드를 짧게 만드는 것이 아니라, 실행할 동작과 필요한 상태를 하나의 객체로 묶어 전달하는 것이다.**

### 1.2. 람다를 사용하지 않는 것이 좋은 경우

람다가 항상 일반 함수보다 좋은 것은 아니다.

다음과 같은 상황에서는 일반 함수 또는 직접적인 코드 구현이 더 적합할 수 있다.

- 단순 계산을 한 번 수행하는 경우
- 여러 함수나 파일에서 동일한 로직을 재사용하는 경우
- 람다 내부의 로직이 길고 복잡한 경우
- 캡처하는 변수가 많아 의존성을 파악하기 어려운 경우

**불필요한 람다 예제**

```cpp
int a = 10;
int b = 20;

auto add = [](int x, int y) {
    return x + y;
};

int result = add(a, b);
```

**일반적인 구현**

```cpp
int a = 10;
int b = 20;

int result = a + b;
```

위와 같이 단순 계산을 즉시 수행하는 상황에서는 람다를 사용할 이유가 거의 없다.

---

## 2. 람다 기본 문법

### 2.1. 기본 구조

```cpp
[capture](parameters) -> return_type {
    // 실행할 코드
};
```

| 구성 요소 | 설명 |
|---|---|
| `[]` | 캡처 목록 |
| `()` | 매개변수 |
| `-> return_type` | 반환 타입 |
| `{}` | 함수 본문 |

반환 타입은 대부분 컴파일러가 추론하므로 생략할 수 있다.

### 2.2. 기본 예제

```cpp
#include <iostream>
using namespace std;

int main()
{
    auto add = [](int a, int b) {
        return a + b;
    };

    cout << add(10, 20); // 30
}
```

**동작 설명**

1. `[]` : 외부 지역변수를 캡처하지 않는다.
2. `(int a, int b)` : 두 개의 매개변수를 정의한다.
3. `return a + b` : 두 값을 더한 결과를 반환한다.
4. `auto add` : 생성된 람다 객체를 저장한다.
5. `add(10, 20)` : 람다 객체의 호출 연산자를 실행한다.

### 2.3. 반환 타입 지정

```cpp
auto divide = [](double a, double b) -> double {
    return a / b;
};

cout << divide(10, 4); // 2.5
```

`-> double`을 사용하면 반환 타입을 명시할 수 있다.

### 2.4. 즉시 실행 람다

```cpp
int result = [](int a, int b) {
    return a + b;
}(10, 20);

// result = 30
```

람다를 변수에 저장하지 않고 정의와 동시에 실행하는 방식이다.

---

## 3. 람다 캡처(Capture)

캡처는 람다가 선언된 위치의 외부 지역변수를 람다 내부에서 사용할 수 있도록 하는 기능이다.

캡처는 크게 **값 캡처(Value Capture)** 와 **참조 캡처(Reference Capture)** 로 구분한다.

### 3.1. 캡처 종류

| 캡처 문법 | 의미 |
|---|---|
| `[]` | 외부 변수 캡처 없음 |
| `[x]` | x를 값으로 캡처 |
| `[&x]` | x를 참조로 캡처 |
| `[=]` | 사용하는 외부 지역변수를 기본적으로 값으로 캡처 |
| `[&]` | 사용하는 외부 지역변수를 기본적으로 참조로 캡처 |
| `[x, &y]` | x는 값, y는 참조로 캡처 |
| `[&, x]` | 기본 참조 캡처, x만 값 캡처 |
| `[=, &x]` | 기본 값 캡처, x만 참조 캡처 |

### 3.2. 값 캡처(Value Capture)

```cpp
int x = 10;

auto func = [x]() {
    cout << x;
};

x = 20;

func(); // 10
```

**동작 원리**

1. 람다 객체가 생성될 때 `x`의 값인 10을 복사한다.
2. 외부 변수 `x`를 20으로 변경한다.
3. 람다 내부에는 복사된 값 10이 유지된다.
4. 따라서 출력 결과는 10이다.

**값 캡처는 람다가 실행되는 시점이 아니라 생성되는 시점에 값을 복사한다.**

### 3.3. 참조 캡처(Reference Capture)

```cpp
int x = 10;

auto func = [&x]() {
    x = 30;
};

func();

cout << x; // 30
```

**동작 원리**

1. `[&x]`를 통해 외부 변수 `x`를 참조한다.
2. 람다 내부에서 원본 변수에 접근한다.
3. 람다가 `x`를 30으로 변경한다.
4. 외부 변수 `x`도 30으로 변경된다.

### 3.4. 값 캡처와 참조 캡처 비교

| 구분 | 값 캡처 | 참조 캡처 |
|---|---|---|
| 문법 | `[x]` | `[&x]` |
| 데이터 | 값 복사 | 원본 변수 참조 |
| 원본 변경 반영 | 안 됨 | 반영됨 |
| 람다에서 원본 수정 | 불가능 | 가능 |
| 원본 변수 수명 의존 | 일반적으로 없음 | 있음 |
| 주의사항 | 복사 비용 | 댕글링 참조 |

---

## 4. 캡처와 매개변수의 차이

람다는 외부 변수를 반드시 캡처해야 하는 것은 아니다.

일반 함수처럼 매개변수로 값을 전달할 수도 있다.

### 4.1. 매개변수로 전달하는 방법

```cpp
int threshold = 100;

auto check = [](int value, int threshold) {
    return value > threshold;
};

cout << check(150, threshold); // 1
```

- `value` : 호출 시 전달되는 매개변수
- `threshold` : 호출 시 전달되는 매개변수
- 캡처를 사용하지 않아도 정상적으로 동작한다.

### 4.2. 캡처를 사용하는 방법

```cpp
int threshold = 100;

auto check = [threshold](int value) {
    return value > threshold;
};

cout << check(150); // 1
```

- `threshold` : 람다 생성 시 캡처한 값
- `value` : 람다 호출 시 전달되는 매개변수

### 4.3. 캡처가 필요한 대표적인 상황

STL의 `std::find_if()`는 조건 함수를 호출할 때 각 원소를 인자로 전달한다.

```cpp
#include <algorithm>
#include <vector>

using namespace std;

int main()
{
    vector<int> data = {10, 50, 120, 150};

    int threshold = 100;

    auto it = find_if(
        data.begin(),
        data.end(),
        [threshold](int value) {
            return value > threshold;
        }
    );
}
```

`find_if()` 내부의 동작을 단순화하면 다음과 같다.

```cpp
for (auto it = first; it != last; ++it)
{
    if (predicate(*it))
    {
        return it;
    }
}
```

`find_if()`는 조건 함수에 원소 하나만 전달한다.

따라서 다음과 같은 람다는 사용할 수 없다.

```cpp
[](int value, int threshold) {
    return value > threshold;
}
```

조건 함수에 두 개의 매개변수가 필요하지만 `find_if()`는 하나만 전달하기 때문이다.

이럴 때 `threshold`를 캡처하면 추가 매개변수 없이 필요한 상태를 사용할 수 있다.

**핵심 차이**

- 캡처: 람다 객체가 생성될 때 외부 상태를 보관하거나 참조
- 매개변수: 람다 함수가 호출될 때마다 전달받는 값

---

## 5. mutable 키워드

값 캡처한 변수는 기본적으로 람다 내부에서 수정할 수 없다.

이는 기본적인 람다의 `operator()`가 `const`이기 때문이다.

### 5.1. 수정할 수 없는 예제

```cpp
int x = 10;

auto func = [x]() {
    x = 20; // 컴파일 오류
};
```

### 5.2. mutable 사용

```cpp
int x = 10;

auto func = [x]() mutable {
    x += 10;
    cout << x << " ";
};

func(); // 20
func(); // 30
func(); // 40

cout << x; // 10
```

**동작 설명**

1. 람다 객체 내부에 `x = 10`이 복사된다.
2. `mutable`을 통해 내부 복사본을 수정할 수 있다.
3. 람다 호출마다 복사본의 값이 증가한다.
4. 외부 원본 변수 `x`는 변경되지 않는다.

---

## 6. 람다의 내부 구조(Closure)

람다는 단순한 함수 포인터가 아니라 컴파일러가 생성하는 함수 객체이다.

### 6.1. 람다 코드

```cpp
int x = 10;

auto func = [x](int a) {
    return x + a;
};

cout << func(20); // 30
```

### 6.2. 컴파일러 내부 구현 개념

개념적으로 다음과 같은 클래스와 유사하다.

```cpp
class Lambda
{
private:
    int x;

public:
    Lambda(int value) : x(value) {}

    int operator()(int a) const
    {
        return x + a;
    }
};
```

사용 예제:

```cpp
Lambda func(10);

cout << func(20); // 30
```

**동작 원리**

1. 람다 표현식에 대응하는 고유한 클로저 타입이 만들어진다.
2. 값 캡처한 변수는 객체 내부에 저장된다.
3. `operator()`가 호출 동작을 구현한다.
4. 객체를 일반 함수처럼 `func(20)`으로 호출할 수 있다.

실제 클로저 타입의 멤버 구성과 배치는 컴파일러 구현에 따라 달라질 수 있다.

### 6.3. auto를 사용하는 이유

각 람다 표현식에는 고유한 익명 타입이 존재한다.

```cpp
auto a = [](int x) {
    return x + 1;
};

auto b = [](int x) {
    return x + 1;
};
```

두 람다의 구현이 같더라도 서로 다른 람다 표현식이므로 타입이 다르다.

일반적으로 타입 이름을 직접 작성할 수 없으므로 `auto`를 사용한다.

---

## 7. 람다와 함수 포인터

### 7.1. 일반 함수 포인터

```cpp
int Add(int a, int b)
{
    return a + b;
}

int main()
{
    int (*func)(int, int) = Add;

    cout << func(10, 20); // 30
}
```

함수 포인터는 일반 함수의 주소를 저장한다.

### 7.2. 캡처가 없는 람다

```cpp
int (*func)(int, int) =
    [](int a, int b) {
        return a + b;
    };

cout << func(10, 20); // 30
```

캡처가 없는 일반 람다는 호환되는 함수 포인터로 변환할 수 있다.

### 7.3. 캡처가 있는 람다

```cpp
int x = 10;

int (*func)(int) = [x](int a) {
    return a + x;
};
```

위 코드는 컴파일 오류가 발생한다.

캡처가 있는 람다는 내부 상태를 보유하므로 일반 함수 포인터로 변환할 수 없다.

### 7.4. std::function 사용

```cpp
#include <functional>
#include <iostream>

using namespace std;

int main()
{
    int x = 10;

    std::function<int(int)> func =
        [x](int a) {
            return a + x;
        };

    cout << func(20); // 30
}
```

`std::function`은 호환되는 호출 가능 객체를 저장하기 위한 타입 소거 래퍼이다.

| 구분 | auto | std::function |
|---|---|---|
| 타입 | 실제 람다 타입 | 공통 함수 시그니처 |
| 호출 비용 | 최적화에 유리 | 추가 오버헤드 가능 |
| 메모리 할당 | 람다 자체에 필수 아님 | 상황에 따라 힙 할당 |
| 활용 | 일반적인 람다 사용 | 콜백 저장 및 전달 |

---

## 8. STL에서 람다 활용

### 8.1. sort - 정렬

```cpp
#include <algorithm>
#include <vector>

using namespace std;

int main()
{
    vector<int> data = {5, 2, 8, 1, 9};

    sort(
        data.begin(),
        data.end(),
        [](int a, int b) {
            return a > b;
        }
    );
}
```

결과:

```text
9 8 5 2 1
```

- `a > b` : 내림차순
- `a < b` : 오름차순

### 8.2. find_if - 조건 검색

```cpp
vector<int> data = {3, 7, 12, 15, 20};

auto it = find_if(
    data.begin(),
    data.end(),
    [](int x) {
        return x > 10;
    }
);

// *it == 12
```

람다가 `true`를 반환하는 첫 번째 원소를 찾는다.

### 8.3. count_if - 조건에 맞는 개수 계산

```cpp
vector<int> data = {1, 2, 3, 4, 5, 6};

int count = count_if(
    data.begin(),
    data.end(),
    [](int x) {
        return x % 2 == 0;
    }
);

// count = 3
```

### 8.4. for_each - 반복 처리

```cpp
vector<int> data = {1, 2, 3, 4, 5};

for_each(
    data.begin(),
    data.end(),
    [](int x) {
        cout << x * 2 << " ";
    }
);
```

출력:

```text
2 4 6 8 10
```

---

## 9. 실무 활용 예제

### 9.1. 패킷 필터링

```cpp
#include <algorithm>
#include <cstdint>
#include <iostream>
#include <vector>

using namespace std;

struct Packet
{
    uint32_t sequence;
    uint16_t messageId;
    bool valid;
};

int main()
{
    vector<Packet> packets = {
        {1, 100, true},
        {2, 200, false},
        {3, 100, true},
        {4, 300, true}
    };

    uint16_t targetId = 100;

    auto it = find_if(
        packets.begin(),
        packets.end(),
        [targetId](const Packet& packet) {
            return packet.messageId == targetId
                && packet.valid;
        }
    );

    if (it != packets.end())
    {
        cout << it->sequence; // 1
    }
}
```

**동작 설명**

1. 패킷 목록을 준비한다.
2. 검색할 메시지 ID를 지정한다.
3. 람다에서 `targetId`를 값으로 캡처한다.
4. `find_if()`가 패킷을 순서대로 검사한다.
5. 조건을 만족하는 첫 번째 패킷을 반환한다.

### 9.2. 멀티스레드 작업

```cpp
#include <iostream>
#include <thread>

using namespace std;

int main()
{
    int value = 10;

    thread worker([value]() {
        cout << value;
    });

    worker.join();
}
```

- `std::thread`에 실행할 람다를 전달한다.
- 새로운 스레드에서 람다를 실행한다.
- `join()`을 통해 작업 스레드가 종료될 때까지 대기한다.

---

## 10. 람다 사용 시 주의사항

### 10.1. 댕글링 참조(Dangling Reference)

참조 캡처한 변수가 소멸한 후 람다가 접근하면 문제가 발생한다.

```cpp
#include <functional>

std::function<int()> CreateFunc()
{
    int x = 10;

    return [&x]() {
        return x;
    };
}

int main()
{
    auto func = CreateFunc();

    int value = func(); // 정의되지 않은 동작
}
```

`CreateFunc()`가 종료되면서 `x`가 소멸하기 때문이다.

**해결 방법: 값 캡처**

```cpp
std::function<int()> CreateFunc()
{
    int x = 10;

    return [x]() {
        return x;
    };
}
```

### 10.2. 데이터 레이스(Data Race)

여러 스레드에서 동일한 변수를 참조 캡처해 동시에 접근할 경우 데이터 레이스가 발생할 수 있다.

공유 변수에 대한 동기화가 필요하다면 `std::mutex` 또는 `std::atomic` 등을 사용해야 한다.

### 10.3. 과도한 람다 사용

```cpp
void Process()
{
    int value = 10;

    auto step1 = [&]() {
        value += 10;
    };

    auto step2 = [&]() {
        value *= 2;
    };

    step1();
    step2();
}
```

위 코드처럼 단순한 순차 실행만 필요하다면 다음 구현이 더 명확하다.

```cpp
void Process()
{
    int value = 10;

    value += 10;
    value *= 2;
}
```

---

## 11. C++ 버전별 람다 기능

| C++ 버전 | 주요 기능 |
|---|---|
| C++11 | 람다 도입, 값/참조 캡처, mutable |
| C++14 | 제네릭 람다, 초기화 캡처 |
| C++17 | `*this` 캡처, constexpr 람다 |
| C++20 | 명시적 템플릿 매개변수 등 |
| C++23 | 명시적 객체 매개변수, static 호출 연산자 등 |

### 11.1. 제네릭 람다(C++14)

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};

cout << add(10, 20);   // 30
cout << add(1.5, 2.5); // 4.0
```

`auto`를 통해 다양한 타입의 매개변수를 처리할 수 있다.

### 11.2. 초기화 캡처(C++14)

```cpp
int x = 10;

auto func = [y = x * 2]() {
    cout << y;
};

func(); // 20
```

캡처 목록에서 새로운 변수를 초기화할 수 있다.

---

## 12. 일반 함수와 람다 비교

| 구분 | 일반 함수 | 람다 |
|---|---|---|
| 이름 | 명시적인 이름 | 익명 |
| 지역변수 캡처 | 불가능 | 가능 |
| 재사용 | 공통 로직에 적합 | 지역적 재사용에 적합 |
| STL 알고리즘 | 사용 가능 | 사용 편리 |
| 콜백 함수 | 사용 가능 | 사용 편리 |
| 코드 위치 | 별도 함수 정의 | 필요한 위치 |
| 유지보수 | 복잡한 로직에 유리 | 짧은 로직에 유리 |

### 선택 기준

**일반 함수를 사용하는 경우**

- 여러 곳에서 재사용해야 하는 로직
- 복잡한 비즈니스 로직
- 독립적인 단위 테스트가 필요한 기능

**람다를 사용하는 경우**

- STL 정렬 및 조건 검색
- 이벤트 콜백
- 특정 상태를 보관해야 하는 함수 객체
- 비동기 또는 멀티스레드 작업
- 특정 위치에서만 사용되는 짧은 동작

**직접 구현하는 경우**

- 단순 계산
- 즉시 실행되는 순차 로직
- 함수를 다른 곳에 전달할 필요가 없는 경우

---

## 13. 면접 예상 질문

### Q1. C++ 람다란 무엇인가?

C++11부터 도입된 익명 함수 객체로, 컴파일러가 생성하는 클로저 타입의 `operator()`를 통해 호출되는 기능이다.

### Q2. 람다를 사용하는 이유는?

실행할 동작을 다른 함수에 전달하거나, 외부 상태를 캡처하여 나중에 실행할 수 있도록 저장하기 위해 사용한다.

### Q3. 값 캡처와 참조 캡처의 차이는?

값 캡처는 람다 생성 시점에 외부 변수의 값을 복사하고, 참조 캡처는 외부 원본 변수를 참조한다.

### Q4. 캡처 대신 매개변수를 전달하면 안 되는가?

가능하다. 그러나 STL 알고리즘처럼 호출 시그니처가 정해진 함수에 전달할 경우 캡처를 활용하여 추가 상태를 보관할 수 있다.

### Q5. mutable은 왜 사용하는가?

값으로 캡처한 변수의 내부 복사본을 람다에서 수정하기 위해 사용한다.

### Q6. 람다와 함수 포인터의 차이는?

함수 포인터는 함수의 주소를 저장하지만 람다는 상태를 보관할 수 있는 함수 객체이다. 일반적인 캡처 람다는 함수 포인터로 변환할 수 없다.

### Q7. 람다 사용 시 주의사항은?

참조 캡처의 객체 수명, 비동기 처리에서의 데이터 레이스, 과도한 람다 사용에 따른 가독성 저하 등을 주의해야 한다.

---

## 14. Key Takeaways (핵심 요약)

1. **Lambda** : C++11부터 도입된 익명 함수 객체이다.
2. **Closure** : 컴파일러가 생성하는 고유한 클로저 타입이다.
3. **Capture** : 외부 지역변수의 값을 복사하거나 참조한다.
4. **Value Capture `[x]`** : 람다 생성 시점의 값을 복사한다.
5. **Reference Capture `[&x]`** : 외부 원본 변수를 참조한다.
6. **mutable** : 값 캡처된 내부 복사본을 수정할 수 있게 한다.
7. **operator()** : 람다 객체가 함수처럼 호출되는 원리이다.
8. **STL** : sort, find_if, count_if 등과 함께 사용된다.
9. **Callback** : 실행할 동작을 등록하고 나중에 실행할 때 유용하다.
10. **Lifetime** : 참조 캡처에서는 외부 변수의 수명을 주의해야 한다.

### 최종 결론

> 람다는 일반 함수를 대체하기 위한 기능이 아니라, 실행할 동작과 필요한 상태를 하나의 함수 객체로 묶어 전달하기 위한 기능이다.

단순한 순차 실행에는 일반 코드가 더 적합할 수 있으며, 여러 곳에서 재사용하는 복잡한 로직에는 일반 함수를 사용하는 것이 좋다.

반면 STL 알고리즘, 콜백, 비동기 작업처럼 **동작 자체를 다른 함수에 전달해야 하는 상황**에서는 람다가 유용하다.
