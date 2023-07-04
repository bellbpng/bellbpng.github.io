---
layout: single
title:  "프로그래머스 - 야근 지수 (Lv.3)"
excerpt: "연습문제"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 설명
- 야근 피로도: n시간 동안 일하고 남은 일의 양을 제곱하여 더한 값
- 퇴근까지 n시간이 남았을 때, 야근 피로도의 최솟값을 계산하라.

### 구현 (TC Pass, 효율성 테스트 시간초과)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

bool cmp(int a, int b){
    return a>b;
}

long long solution(int n, vector<int> works) {
    long long answer = 0;
    
    int total_load = 0;
    for(int i=0; i<works.size(); i++){
        total_load += works[i];
    }
    
    // 야근을 할 필요가 없는 경우
    if(total_load <= n) return answer;
    
    while(n>0){
        sort(works.begin(), works.end(), cmp);
        works[0] -= 1;
        n -= 1;
    }
    
    for(auto ele:works){
        answer += ele * ele;
    }
    
    return answer;
}

```
- 최악의 경우: n이 100만, works는 2만인 경우 길이가 2만인 배열을 100만번 정렬해야 한다.

#### 개선한 점
- 로직은 같으나, sort 함수로 매번 졍럴하는 것이 아니라 우선순위 큐를 활용하여 최대 힙 구조를 활용할 수 있도록 한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>
#include <queue>

using namespace std;

long long solution(int n, vector<int> works) {
    long long answer = 0;
    
    int total_load = 0;
    for(int i=0; i<works.size(); i++){
        total_load += works[i];
    }
    
    // 야근을 할 필요가 없는 경우
    if(total_load <= n) return answer;
    
    priority_queue<int> pq; // max heap(최댓값이 top에 있도록)
    for(auto ele:works)
        pq.push(ele);
    
    int ele;
    while(n>0){
        ele = pq.top();
        pq.pop();
        pq.push(ele-1);
        n -= 1;
    }
    
   while(!pq.empty()){
       ele = pq.top();
       pq.pop();
       answer += ele*ele;
   }
    
    return answer;
}
```
- priority_queue 는 기본 설정은 최대 힙(Top에 최댓값이 위치)이고, **greater<int>** 를 비교 인자로 전달하면 최소 힙(Top에 최솟값이 위치)을 구성한다.
- 우선순위 큐에 들어가는 비교 인자는 현재 원소와 새로 들어온 원소의 우선순위를 비교하는 인자이고, 아래와 같이 커스텀 할 수 있다.
- 벡터의 원소들을 정렬할 때 사용하는 비교인자 작성 방법과는 차이가 있다.
```c++

// 벡터 정렬 비교인자 작성
bool cmp(int a, int b){
    return a < b; // 오름차순 정렬
}

```
- v[0] = a, v[1] = b 라고 할 때, 위 구문은 a < b 결과가 false 이면 swap을 하도록 동작한다.
- 따라서, a보다 작은 값인 b가 들어오는 경우 swap 이 되면서 오름차순으로 정렬된다.

```c++
// 우선순위 큐의 비교인자 작성
struct cmp{
    bool operator()(int a, int b){
        return a<b; // 내림차순 정렬
    }
};

```
- 우선순위 큐의 경우 원래 있던 a가 새로 입력 받은 b보다 작은 경우 true를 반환하면서 b에게 높은 우선순위를 부여하게 된다.
- 따라서, b가 a보다 앞쪽(Top)에 위치하게 되어 결과적으로 내림차순 정렬이 되는 것이다.
