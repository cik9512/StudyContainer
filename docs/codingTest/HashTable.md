# Chapter 01. Hash Table

## 1. Hash Table Concept

### 1.1. What is a Hash Table?

Hash Table은 Key-Value 형태로 데이터를 저장하고 빠르게 검색하기 위한 자료구조이다.

Hash Function을 사용하여 Key를 Hash Value로 변환하고, 이를 기반으로 데이터의 저장 위치를 결정한다.

**특징**
- 평균 삽입: O(1)
- 평균 검색: O(1)
- 평균 삭제: O(1)
- 최악의 경우: O(n) — 해시 충돌이 심한 경우
- 원소의 저장 순서가 보장되지 않음

### 1.2. Hash Collision

서로 다른 Key가 동일한 Hash 위치에 대응되는 현상을 Hash Collision이라고 한다.

대표적인 충돌 해결 방법:
- Separate Chaining: 동일한 버킷에 여러 원소를 저장
- Open Addressing: 충돌 시 다른 저장 위치를 탐색

C++의 `unordered_map`, `unordered_set`은 충돌을 처리하는 해시 기반 컨테이너다.

---

## 2. C++ Hash Table Containers

### 2.1. unordered_map

`unordered_map<Key, Value>`는 Key와 Value를 쌍으로 저장한다.

```cpp
#include <string>
#include <unordered_map>
using namespace std;

unordered_map<string, int> scores;

scores["Alice"] = 90;
scores["Bob"] = 85;

scores["Alice"]++; // 91
```

주요 함수:

| 함수 | 설명 | 평균 복잡도 |
|---|---|---|
| `map[key]` | 값 접근, Key가 없으면 생성 | O(1) |
| `map.find(key)` | Key 검색 | O(1) |
| `map.count(key)` | Key 존재 여부 (0 또는 1) | O(1) |
| `map.insert({key, value})` | 원소 삽입 | O(1) |
| `map.erase(key)` | 원소 삭제 | O(1) |
| `map.size()` | 원소 개수 반환 | O(1) |

**주의: `map[key]`는 Key가 없으면 새로운 원소를 생성한다.**

```cpp
unordered_map<int, int> m;

int value = m[10];
// Key 10이 생성되고 Value는 0으로 초기화됨
```

따라서 단순히 Key 존재 여부만 확인하려면 `find()`를 사용하는 것이 좋다.

### 2.2. unordered_set

`unordered_set<Key>`은 Key만 저장하고 중복을 허용하지 않는다.

```cpp
#include <unordered_set>
using namespace std;

unordered_set<int> numbers;

numbers.insert(10);
numbers.insert(20);
numbers.insert(10);

// 저장된 원소: 10, 20
```

**unordered_map vs unordered_set**

| 구분 | unordered_map | unordered_set |
|---|---|---|
| 데이터 | Key-Value | Key |
| 중복 Key | 불가 | 불가 |
| 주요 목적 | 값 조회, 빈도 계산 | 중복 검사, 존재 확인 |
| 평균 검색 | O(1) | O(1) |

### 2.3. find() and end()

```cpp
unordered_set<int> numbers = {10, 20, 30};

if (numbers.find(20) != numbers.end())
{
    // 20이 존재함
}
```

- `find(20)`: 20을 찾으면 해당 원소의 Iterator 반환
- `end()`: 마지막 원소 다음 위치의 Iterator
- 검색 실패 시 `end()` 반환

**C++ 버전 주의**

```cpp
// C++17
if (numbers.find(20) != numbers.end())

// C++20
if (numbers.contains(20))
```

`contains()`는 C++20부터 지원한다.

---

## 3. Core Problem-Solving Skills

### 3.1. Frequency Counting

각 원소가 몇 번 등장했는지 계산하는 기법이다.

```cpp
string s = "aabbc";

unordered_map<char, int> freq;

for (char ch : s)
{
    freq[ch]++;
}
```

결과:

```text
a: 2
b: 2
c: 1
```

핵심은 `freq[ch]++`이다.

Key가 처음 등장하면 정수 Value는 0으로 초기화되고, 이후 1씩 증가한다.

**활용 문제:** Valid Anagram, 문자 빈도 비교, 중복 횟수 계산

### 3.2. Duplicate Detection

해시셋을 사용하여 이전에 등장한 값인지 확인한다.

```cpp
unordered_set<int> seen;

for (int num : nums)
{
    if (seen.find(num) != seen.end())
        return true;

    seen.insert(num);
}

return false;
```

핵심은 **검색 후 삽입**이다.

- 이미 존재하면 중복
- 존재하지 않으면 삽입
- 반복문은 하나만 필요

### 3.3. Complement Lookup

목표값을 만들기 위해 필요한 나머지 값을 검색하는 기법이다.

```cpp
int complement = target - nums[i];

auto it = seen.find(complement);

if (it != seen.end())
{
    return {it->second, i};
}
```

예를 들어 `target = 9`, `nums[i] = 7`이라면 필요한 값은 `2`이다.

전체 배열에서 2를 다시 탐색하는 대신 해시맵에서 평균 O(1)로 검색한다.

**활용 문제:** Two Sum, 특정 합을 만드는 숫자 쌍 탐색

### 3.4. Choosing the Right Container

문제에서 필요한 정보에 따라 컨테이너를 선택한다.

- 원소 존재 여부만 필요 → `unordered_set`
- 원소의 개수가 필요 → `unordered_map<Key, int>`
- 원소의 인덱스가 필요 → `unordered_map<Value, Index>`
- 동일한 그룹으로 묶기 → `unordered_map<Key, vector<Value>>`

---

## 4. Solved Problems

### 4.1. Two Sum — LeetCode 1

**Problem**

정수 배열에서 두 원소의 합이 target이 되는 인덱스 두 개를 반환한다.

```text
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
```

**Solution**

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target)
    {
        unordered_map<int, int> seen;

        for (int i = 0; i < nums.size(); i++)
        {
            int complement = target - nums[i];

            auto it = seen.find(complement);

            if (it != seen.end())
                return {it->second, i};

            seen[nums[i]] = i;
        }

        return {};
    }
};
```

**Key Points**

1. 필요한 숫자 `target - nums[i]`를 계산한다.
2. 해시맵에서 이전에 등장했는지 확인한다.
3. 찾았다면 두 인덱스를 반환한다.
4. 없다면 현재 숫자와 인덱스를 저장한다.

검색 후 삽입하므로 동일한 인덱스를 두 번 사용하지 않는다.

- Time Complexity: O(n) 평균
- Space Complexity: O(n)

### 4.2. Valid Anagram — LeetCode 242

**Problem**

두 문자열이 동일한 문자를 동일한 개수만큼 포함하는지 판단한다.

```text
Input: s = "anagram", t = "nagaram"
Output: true
```

**Solution — My Approach**

```cpp
#include <string>
#include <unordered_map>
using namespace std;

class Solution {
public:
    bool isAnagram(string s, string t)
    {
        unordered_map<char, int> s_map;
        unordered_map<char, int> t_map;

        for (char ch : s)
            s_map[ch]++;

        for (char ch : t)
            t_map[ch]++;

        return s_map == t_map;
    }
};
```

**Key Points**

- 각 문자의 등장 횟수를 계산한다.
- 두 `unordered_map`은 `==` 연산자로 비교할 수 있다.
- Key와 Value가 모두 동일해야 true이다.

**Optimization**

두 문자열의 길이가 다르면 즉시 false를 반환할 수 있다.

```cpp
if (s.size() != t.size())
    return false;
```

문자열이 영문 소문자로 제한되어 있기 때문에 크기 26의 배열로도 해결할 수 있다.

- Time Complexity: O(n + m) 평균
- Space Complexity: O(1), 최대 26종류 문자

### 4.3. Contains Duplicate — LeetCode 217

**Problem**

정수 배열에서 같은 숫자가 두 번 이상 등장하는지 판단한다.

```text
Input: nums = [1, 2, 3, 1]
Output: true
```

**Solution — My Final Code**

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums)
    {
        unordered_set<int> seen;

        for (int i = 0; i < nums.size(); i++)
        {
            if (seen.find(nums[i]) != seen.end())
                return true;

            seen.insert(nums[i]);
        }

        return false;
    }
};
```

**Key Points**

- 중복 여부만 필요하므로 `unordered_set`을 사용한다.
- 이미 존재하면 즉시 true를 반환한다.
- 없으면 추가하고 다음 숫자를 검사한다.

- Time Complexity: O(n) 평균
- Space Complexity: O(n)

---

## 5. Mistakes & Lessons Learned

### Mistake 1. C++17 vs C++20

```cpp
// C++20 only
seen.contains(value);

// C++17 compatible
seen.find(value) != seen.end();
```

코딩테스트에서 사용하는 C++ 버전에 맞춰 문법을 선택해야 한다.

### Mistake 2. Hash Map vs Hash Set

값과 함께 추가 정보를 저장해야 한다면 `unordered_map`을 사용한다.

단순한 존재 여부만 필요하다면 `unordered_set`이 더 적합하다.

---

## 6. Complexity Summary

| Problem | Time (Average) | Space |
|---|---|---|
| Two Sum | O(n) | O(n) |
| Valid Anagram | O(n + m) | O(1) |
| Contains Duplicate | O(n) | O(n) |

참고: 해시 조회는 평균 O(1)이지만, 심한 해시 충돌로 최악의 경우 O(n)이 될 수 있다.

---

## 7. Quick Review

**Q1. unordered_map과 unordered_set의 차이점은?**

- map은 Key-Value 저장
- set은 Key만 저장

**Q2. find()가 검색에 실패하면 무엇을 반환하는가?**

- `end()` Iterator

**Q3. unordered_map에서 freq[ch]++가 가능한 이유는?**

- 존재하지 않는 Key에 접근하면 기본 초기화된 Value가 생성되기 때문

**Q4. Two Sum에서 검색 후 삽입하는 이유는?**

- 동일한 원소를 두 번 사용하지 않기 위해서

**Q5. unordered_set의 평균 검색 시간복잡도는?**

- O(1)

---

## 8. Final Takeaways

1. 중복 검사에는 `unordered_set`을 우선 고려한다.
2. 빈도 계산에는 `unordered_map<Key, int>`를 활용한다.
3. 빠른 검색이 필요하면 해시 자료구조를 고려한다.
4. `find()`와 `end()`의 관계를 확실하게 이해한다.
5. 해시를 활용하면 O(n²) 탐색을 평균 O(n)으로 줄일 수 있는 문제가 많다.
6. 코딩테스트에서는 정확성뿐 아니라 시간복잡도와 공간복잡도도 분석한다.
