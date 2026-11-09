# C++ 자료구조(Data Structure) 정리

## 1. Array vs Vector

| 구분 | Array (`int arr[10]`) | Vector (`std::vector`) |
|---|---|---|
| 크기 | 고정 | 동적으로 변경 가능 |
| 메모리 | 연속된 공간 | 연속된 공간 |
| 인덱스 접근 | O(1) | O(1) |
| 중간 삽입·삭제 | O(n) | O(n) |
| 끝에 추가 | 직접 관리 | 평균 O(1) |

```cpp
int arr[5] = {1, 2, 3, 4, 5};

vector<int> v = {1, 2, 3};
v.push_back(4);  // 끝에 추가
v.pop_back();    // 끝 원소 제거
v[0];            // 첫 번째 원소
v.size();        // 원소 개수
v.capacity();    // 확보된 저장 용량
```

- `size()`: 현재 저장된 원소 개수
- `capacity()`: 재할당 없이 저장 가능한 원소 개수
- 용량 부족 시 더 큰 메모리를 할당하고 기존 원소를 이동·복사
- 검색은 정렬되지 않은 경우 O(n)

---

## 2. 대표 자료구조

| 자료구조 | 특징 | C++ STL |
|---|---|---|
| Array | 고정 크기, 연속 메모리 | `array` |
| Vector | 크기 변경 가능한 동적 배열 | `vector` |
| Linked List | 노드를 포인터로 연결 | `list`, `forward_list` |
| Stack | LIFO, 나중에 넣은 데이터부터 제거 | `stack` |
| Queue | FIFO, 먼저 넣은 데이터부터 제거 | `queue` |
| Deque | 앞뒤 삽입·삭제에 유리 | `deque` |
| Circular Queue | 인덱스를 순환시켜 공간 재사용 | 직접 구현 |
| Hash Table | 해시 기반 빠른 탐색 | `unordered_map`, `unordered_set` |
| Tree | 계층 구조 | `map`, `set` 등 |
| Heap | 최댓값·최솟값 관리 | `priority_queue` |
| Graph | 정점과 간선의 관계 | `vector` 등으로 구현 |

### Stack — LIFO

```cpp
stack<int> s;
s.push(10);
s.push(20);
s.top();  // 20
s.pop();  // 20 제거
```

- 나중에 들어간 데이터가 먼저 나옴
- 삽입·삭제 O(1)

### Queue — FIFO

```cpp
queue<int> q;
q.push(10);
q.push(20);
q.front(); // 10
q.pop();   // 10 제거
```

- 먼저 들어간 데이터가 먼저 나옴
- 삽입·삭제 O(1)

### Circular Queue — 순환 큐

```cpp
int arr[10];
int front = 0;
int rear = 0;

rear = (rear + 1) % 10;
front = (front + 1) % 10;
```

- 일반적으로 고정 크기 배열 사용
- 끝에 도달하면 인덱스를 0으로 순환
- 빈 공간을 재사용하며 FIFO 유지
- 삽입·삭제 O(1), 가득 찬 상태 관리 필요

### Linked List — 연결 리스트

```cpp
list<int> l = {10, 20, 30};
l.push_back(40);
l.push_front(5);
```

- 노드가 포인터로 연결됨
- 임의 인덱스 접근 O(n)
- 위치를 알고 있을 때 삽입·삭제 O(1)
- 연속 메모리가 아니므로 캐시 효율이 낮을 수 있음

---

## 3. Map과 Set

### Map — Key와 Value 저장

```cpp
unordered_map<string, int> m;

m["A"] = 10;
m["B"] = 20;

cout << m["A"]; // 10
```

### Set — 중복 없는 원소 저장

```cpp
unordered_set<int> s;

s.insert(10);
s.insert(20);
s.insert(10); // 중복 삽입 안 됨
```

| 구분 | map / set | unordered_map / unordered_set |
|---|---|---|
| 내부 구조 | 일반적으로 균형 이진 탐색 트리 | 해시 테이블 |
| 정렬 | 키 기준 정렬 | 정렬 보장 없음 |
| 탐색 | O(log n) | 평균 O(1), 최악 O(n) |
| 삽입·삭제 | O(log n) | 평균 O(1), 최악 O(n) |

---

## 4. Tree / Heap / Graph

**Tree(트리)**
- 부모-자식 관계의 계층형 자료구조
- 이진 탐색 트리(BST): 왼쪽은 작은 값, 오른쪽은 큰 값
- 균형 BST 탐색 O(log n), 편향된 일반 BST는 O(n)

**Heap(힙 자료구조)**
- 부모 노드가 자식보다 크거나(최대 힙) 작은(최소 힙) 구조
- 최댓값·최솟값 조회 O(1)
- 삽입·삭제 O(log n)
- 메모리의 Heap 영역과는 다른 개념

```cpp
priority_queue<int> pq;
pq.push(10);
pq.push(30);
pq.top(); // 30 (최대 힙)
```

**Graph(그래프)**
- 정점(Vertex)과 간선(Edge)으로 구성
- 방향/무방향, 가중치/비가중치 그래프
- BFS(너비 우선 탐색): Queue 사용
- DFS(깊이 우선 탐색): Stack 또는 재귀 사용

---

## 5. 시간 복잡도 요약

| 자료구조 | 인덱스 접근 | 탐색 | 삽입·삭제 |
|---|---|---|---|
| Array / Vector | O(1) | O(n) | 중간 O(n), Vector 끝 평균 O(1) |
| Linked List | O(n) | O(n) | 위치를 알면 O(1) |
| Stack | 불가 | 일반적으로 지원 안 함 | O(1) |
| Queue | 불가 | 일반적으로 지원 안 함 | O(1) |
| Hash Table | 키 기반 평균 O(1) | 평균 O(1) | 평균 O(1) |
| 균형 BST | 키 기반 O(log n) | O(log n) | O(log n) |
| Heap | 최상위 O(1) | 임의 원소 O(n) | O(log n) |

※ 삽입·삭제 복잡도는 위치 탐색 비용을 제외하거나, 해당 자료구조가 지원하는 위치를 기준으로 함.

---

## 6. 핵심 암기

- **Array:** 고정 크기, 연속 메모리
- **Vector:** 동적 배열, 연속 메모리, 크기 자동 확장
- **Linked List:** 노드 연결, 임의 접근 느림
- **Stack:** LIFO
- **Queue:** FIFO
- **Circular Queue:** 인덱스 순환으로 공간 재사용
- **Deque:** 양쪽 끝 삽입·삭제
- **Hash Table:** 평균 O(1) 탐색
- **Map/Set:** 정렬된 키, O(log n)
- **Heap:** 최댓값·최솟값 관리
- **Tree:** 계층 구조
- **Graph:** 정점과 간선의 관계

**실무에서는 `vector`를 기본 컨테이너로 우선 고려하고, 요구사항에 따라 `map`, `unordered_map`, `queue` 등을 선택한다.**
