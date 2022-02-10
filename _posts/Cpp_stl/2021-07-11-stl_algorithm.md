---
layout: single
title:  "C++ STL정리 - algorithm"
excerpt: "C++ STL - algorithm "

categories:
  - C++ STL
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 헤더파일 algorithm에 속한 함수 정리

### sort 함수
- 정렬 함수

```c
#include <algorithm>
#include <vector>

sort(시작주소, 끝주소, 비교함수)
// 끝주소는 포함하지 않는다.

// 내림차순. 왼쪽에 큰 수가 오도록 정렬
bool cmp1(const int& a, const int& b){
    return a > b;
}

// 오름차순. 왼쪽에 작은 수가 오도록 정렬
bool cmp2(const int& a, const int& b){
    return a < b;
}

// 문자열 사전순으로 정리
bool cmp3(const string&s1, const string&s2){
  return s1 < s2;
}

vector<int> v1(10);
sort(v.begin(), v.end(), cmp1);

// 문자열의 경우 아스키코드값에 따라 정렬된다.
vector<string> v2(10);
sort(v.begin(), v.end(), cmp3);


```

- 컨테이너나 배열의 원소들을 특정 기준에 맞추어 정렬할 때 사용하는 함수이다
- 비교함수 인자 생략 시 오름차순으로 정렬한다.
- 컨테이너의 자료형에 따라 비교함수에 전달되는 인자의 자료형도 달라져야 한다.

```c
typedef struct Point{
  int x;
  int y;
}

// x 값에 따라 오름차순으로 정렬한다. x값이 같다면 y값에 따라 오름차순으로 정렬한다.
bool cmp(const Point& p1, const Point& p2){
  if(p1.x == p2.x){
    return p1.y < p2.y
  }
  else{
    return p1.x < p2.x
  }
}
```

- 구조체를 특정 조건에 따라 정렬하는 방법
- 비교함수를 통해 직접 정의한다.


### 최댓값과 최솟값 찾기
- max_element(시작주소, 끝주소)
- min_element(시작주소, 끝주소)
- 끝주소는 포함하지 않는다.
- 주소를 반환하므로 값을 참조하려면 * 연산자를 이용한다.

```c
#include <algorithm>

vector<int> v(10);
int max = *max_element(v.begin(), v.end());
int min = *min_element(v.begin(), v.end());
```

### binary_search 함수
- 이진탐색 함수

```c
#include <iostream>
#include <algorithm>

binary_search(low_iter, high_iter, key)
// 탐색에 성공하면 (key를 찾으면) true 반환

if(binary_search(v.begin(), v.end(), key)){
  // doing something
}

```

- 이진탐색을 진행하려면 배열이 반드시 정렬되어 있어야한다. (오름차순)
- 시간복잡도는 O(log2(n))

