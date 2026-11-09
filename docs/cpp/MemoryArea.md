# C++ 메모리 구조와 static

## 1. 프로그램 메모리 구조

| 영역 | 저장 내용 | 예시 |
|---|---|---|
| `.text` | 함수의 기계어 코드 | 일반 함수, 멤버 함수 |
| `.rodata` | 읽기 전용 데이터 | 문자열 리터럴, 일부 const |
| `.data` | 0이 아닌 값으로 초기화된 정적 데이터 | `static int a = 10;` |
| `.bss` | 미초기화·0 초기화 정적 데이터 | `static int a = 0;` |
| Stack | 지역변수, 지역 객체, 함수 호출 정보 | `Radar radar;` |
| Heap | 동적 할당 객체 | `new Radar()` |

- `.text`, `.data`, `.bss` 등은 실행 파일의 대표적인 섹션 이름이다.
- `.bss`는 초기값 0을 실행 파일에 모두 기록하지 않아 파일 크기를 줄인다.
- `const`라고 무조건 `.rodata`에 저장되는 것은 아니다.
- 실제 저장 위치는 OS, 컴파일러, 최적화에 따라 달라질 수 있다.

---

## 2. static의 종류

### ① 함수 내부 static 지역변수

```cpp
void Count() {
    static int count = 0;
    cout << ++count << endl;
}

Count(); // 1
Count(); // 2
Count(); // 3
```

- 일반 지역변수: 함수 호출 시 생성, 블록 종료 시 소멸
- static 지역변수: 프로그램 종료까지 수명 유지
- 접근 범위는 함수 내부로 제한
- 동적 초기화는 최초 실행 시 한 번 수행
- static 초기화는 스레드 안전하지만 이후 변수 접근은 별도 동기화 필요

### ② 클래스 static 멤버 변수

```cpp
class Radar {
public:
    int id = 0;
    static int count;
};

int Radar::count = 0;
```

```cpp
Radar a, b;
a.id = 10;
b.id = 20;
Radar::count = 5;
```

- 일반 멤버 변수 `id`: 객체마다 별도 존재
- static 멤버 변수 `count`: 모든 객체가 공유
- static 멤버는 객체 내부에 저장되지 않음
- `sizeof(Radar)`에 static 멤버 크기는 포함되지 않음

### ③ static 멤버 함수

```cpp
class Radar {
    int id;
    static int count;

public:
    static void Increase() {
        count++;
        // id++; // 오류
    }
};

Radar::Increase();
```

- 객체 생성 없이 호출 가능
- `this` 포인터 없음
- static 멤버에는 직접 접근 가능
- 일반 멤버에는 객체 없이 직접 접근 불가
- 함수 코드는 일반적으로 `.text`에 존재

### ④ 전역 static

```cpp
// Radar.cpp
static int count = 10;
```

- 내부 연결(Internal Linkage)을 가짐
- 다른 `.cpp` 파일에서 해당 이름으로 직접 접근 불가
- C++에서는 익명 네임스페이스로도 구현 가능

---

## 3. 클래스와 객체 메모리 구조

```cpp
class Radar {
    int id = 10;
    double range = 100.0;

public:
    static int count;
    void Start() {
        int temp = 5;
    }
};

int Radar::count = 0;
```

```cpp
Radar a;                // 일반적으로 Stack
Radar* b = new Radar(); // 객체는 Heap
```

**메모리 배치**

```text
[Stack]
 ├─ a 객체
 │   ├─ id
 │   └─ range
 └─ b 포인터

[Heap]
 └─ *b 객체
     ├─ id
     └─ range

[Data / BSS]
 └─ Radar::count

[Text]
 └─ Radar::Start()
```

- 일반 멤버 변수는 객체가 저장된 위치에 함께 존재
- 멤버 함수 코드는 객체마다 복사되지 않음
- 함수 내부 지역변수 `temp`는 호출 중 일반적으로 Stack에 존재
- 포인터 멤버는 객체 내부에 있지만, 가리키는 데이터는 별도 위치에 존재할 수 있음

---

## 4. this 포인터

```cpp
class Radar {
    int id;

public:
    void SetId(int value) {
        this->id = value;
    }
};

Radar a, b;
a.SetId(10);
b.SetId(20);
```

- `this`는 현재 멤버 함수를 호출한 객체를 가리키는 포인터
- 같은 함수 코드로 서로 다른 객체의 멤버 변수에 접근 가능
- 일반 비정적 멤버 함수에서 사용
- static 멤버 함수에는 `this` 없음

---

## 5. virtual과 객체 메모리

```cpp
class Parent {
public:
    virtual void Print() {}
    virtual ~Parent() = default;
};

class Child : public Parent {
public:
    void Print() override {}
};
```

- `virtual`: 실제 객체 타입에 따른 동적 바인딩 지원
- `vptr`: 객체와 가상함수 테이블을 연결하는 포인터
- `vtable`: 가상함수 호출 대상 정보 관리
- 가상함수의 실제 코드는 일반적으로 `.text`에 존재
- vptr 때문에 객체 크기가 증가할 수 있음
- vptr/vtable은 대표적인 구현 방식이며 C++ 표준에서 강제하지 않음

---

## 6. sizeof와 메모리 정렬

```cpp
class Example {
    int a;      // 4B
    char b;     // 1B
    double c;   // 8B

    void Print() {}
};
```

대표적인 환경에서:

```text
int a      4B
char b     1B
Padding    3B
double c   8B
--------------
총 16B
```

- Padding: 정렬 조건을 만족하기 위해 추가되는 공간
- 함수 코드는 `sizeof`에 포함되지 않음
- static 멤버 변수도 포함되지 않음
- virtual 사용 시 객체 크기가 증가할 수 있음

---

## 7. 핵심 암기

| 개념 | 핵심 |
|---|---|
| 일반 지역변수 | 블록 실행 중 수명 유지 |
| static 지역변수 | 프로그램 종료까지 수명 유지 |
| 일반 멤버 변수 | 객체마다 독립 |
| static 멤버 변수 | 모든 객체가 공유 |
| static 멤버 함수 | 객체 없이 호출, this 없음 |
| this | 호출 대상 객체를 가리킴 |
| virtual | 동적 바인딩, 일반적으로 vtable 활용 |
| Stack 객체 | 일반 멤버 변수도 Stack에 존재 |
| Heap 객체 | 일반 멤버 변수도 Heap에 존재 |

**핵심: 일반 멤버 변수는 객체를 따라가고, static 멤버 변수는 객체와 독립적으로 존재하며, 멤버 함수 코드는 객체마다 복사되지 않는다.**
