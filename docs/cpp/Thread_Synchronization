
# C++ 멀티스레드 동기화

## 1. Race Condition (경쟁 상태)

여러 스레드의 실행 순서에 따라 프로그램의 결과가 달라지는 현상이다.

특히 공유 데이터에 동기화 없이 접근하여 수정하면 Data Race가 발생할 수 있다.

### 문제 코드

```cpp
int count = 0;

void Work()
{
    count++; // 동기화되지 않은 공유 데이터 수정
}
```

`count++`는 원자적 연산이 아니므로 여러 스레드가 실행하면 잘못된 결과가 발생할 수 있다.

---

## 2. Mutex (Mutual Exclusion, 상호 배제)

여러 스레드가 하나의 공유 자원에 동시에 접근하지 못하도록 잠그는 동기화 객체이다.

- `lock()` : 잠금 획득
- `unlock()` : 잠금 해제
- `lock_guard` : RAII 기반 자동 잠금 관리

### 예제

```cpp
#include <mutex>

int count = 0;
std::mutex mtx;

void Work()
{
    std::lock_guard<std::mutex> lock(mtx);
    count++;
} // 자동으로 unlock
```

**핵심:** 한 번에 하나의 스레드만 임계구역에 접근하도록 제한한다.

---

## 3. Semaphore (세마포어)

내부 카운터를 이용해 동시에 실행 가능한 작업 수를 제한하거나 스레드 간 신호를 전달하는 동기화 객체이다.

- `acquire()` : 허가권 획득
- `release()` : 허가권 반납
- 카운터가 0이면 `acquire()`에서 대기

### 예제 (C++20)

```cpp
#include <semaphore>

std::counting_semaphore<3> sem(3);

void Work()
{
    sem.acquire();

    // 동시에 최대 3개 작업 허용

    sem.release();
}
```

**주의:** Semaphore(3)은 공유 변수의 동시 수정을 막지 않는다. 필요하면 Mutex나 Atomic을 함께 사용한다.

### Mutex와 Semaphore 비교

| 구분 | Mutex | Semaphore |
|---|---|---|
| 목적 | 공유 자원 보호 | 동시 작업 수 제한·신호 전달 |
| 동시 진입 | 1개 | 지정된 개수 |
| 관리 방식 | 잠금 및 소유권 | 카운터 |
| 대표 사용 | 공유 변수 보호 | 연결 풀, 작업 완료 알림 |

---

## 4. Deadlock (교착 상태)

여러 스레드가 서로 상대방이 보유한 자원을 기다리면서 영원히 진행하지 못하는 상태이다.

### 발생 예시

```cpp
#include <mutex>

std::mutex m1, m2;

void Thread1()
{
    std::lock_guard<std::mutex> a(m1);
    std::lock_guard<std::mutex> b(m2);
}

void Thread2()
{
    std::lock_guard<std::mutex> a(m2);
    std::lock_guard<std::mutex> b(m1);
}
```

Thread1은 m1을, Thread2는 m2를 먼저 획득하면 서로의 잠금을 기다리며 Deadlock이 발생할 수 있다.

### 해결 방법

여러 Mutex를 일관된 순서로 획득하거나 `std::scoped_lock`을 사용한다.

```cpp
void Thread1()
{
    std::scoped_lock lock(m1, m2);
}

void Thread2()
{
    std::scoped_lock lock(m1, m2);
}
```

`std::scoped_lock`은 C++17부터 지원한다.

---

## 5. 핵심 요약

| 개념 | 한 줄 정리 |
|---|---|
| Race Condition | 실행 순서에 따라 결과가 달라지는 현상 |
| Mutex | 공유 자원에 대한 상호배제 |
| Semaphore | 허가권으로 동시 작업 수 및 실행 순서 제어 |
| Deadlock | 스레드들이 서로 기다리며 진행하지 못하는 상태 |

### 면접 핵심 암기

- Race Condition 예방 → Mutex, Atomic 등 적절한 동기화
- Mutex → 한 번에 하나의 스레드만 접근
- Semaphore → 최대 N개 작업 허용 또는 신호 전달
- Deadlock 예방 → 잠금 순서 통일, `scoped_lock` 사용
