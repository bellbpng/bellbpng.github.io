---
layout: single
title:  "프로그래머스 - 숫자 게임 (Lv.3)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [숫자게임](https://school.programmers.co.kr/learn/courses/30/lessons/12987)

### 구현(Time Exceed)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

// 인접행렬을 만든다.
// graph[i]는 A의 i번 사원과 대결했을 때 이길 수 있는 카드를 가진 B 사원의 인덱스다.
vector<int> graph[100000];
vector<bool> visited(100000, false);

bool check_available_card(int idx){
    if(graph[idx].size()==0) return false;
    for(int i=0; i<graph[idx].size(); i++){
        int staff = graph[idx][i];
        if(visited[staff]==false) return true;
    }
    return false;
}

int get_score(int idx, int score, int n){
    if(idx==n) return score;
    if(check_available_card(idx)==false)
        return get_score(idx+1, score, n);
    
    int ret = 0;
    for(int i=0; i<graph[idx].size(); i++){
        int staff = graph[idx][i];
        if(visited[staff]==true) continue;
        visited[staff] = true;
        ret = max(ret, get_score(idx+1, score+1, n));
        visited[staff] = false;
    }
    return ret;
}

int solution(vector<int> A, vector<int> B) {
    int answer = -1;
    
    int n = A.size();
    for(int i=0; i<n; i++){
        int a_card = A[i];
        for(int j=0; j<n; j++){
            if(a_card < B[j])
                graph[i].push_back(j);
        }
    }
    
    int a_max = *max_element(A.begin(), A.end());
    int a_min = *min_element(A.begin(), A.end());
    int b_max = *max_element(B.begin(), B.end());
    int b_min = *min_element(B.begin(), B.end());
    
    // N점을 획득하는 경우
    if(a_max < b_min) return n;
    // 0점을 획득하는 경우
    if(a_min > b_max) return 0;
    
    answer = get_score(0, 0, n);
    
    return answer;
}

```

#### 문제점 분석
- 최대 승점을 얻는 로직을 완전탐색(재귀호출, 비록 범위가 정해진 상태이기는 하지만...)을 이용해 구현하였기 때문에 시간복잡도가 높다.
- A팀의 사원과 붙어서 이길 수 없는 상황일 때 -> graph[idx].size()가 0인 상황일 때 아무하고도 상대를 붙여주지 않았다.
- 누구든 상대를 한명 붙여줘야한다.
- 시간복잡도가 O(n^2)이기 때문에 다른 로직 구현이 필요하다.

#### 개선한 점
- 최대 승점을 얻을 수 있는 상황을 다시 생각해보자.
- A의 카드 숫자보다 큰 숫자를 매라운드에서 낼수록 승점이 높다.
- A의 순서는 이미 정해져 있기 때문에 **문제의 근본은 어떻게 A배열과 B배열을 매칭할 것인가?**
- A의 낮은 카드 숫자를 B의 낮은 카드 숫자로 매칭할 때 승점은 최대가 된다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

// 오름차순 정렬
bool cmp(int a, int b)
{
    return a<b;
}

int set_match(vector<int>& A, vector<int>& B){
    int n = A.size();
    int ai=0, bi=0;
    
    int ret = 0;
    while(true){
        if(ai>=n || bi>=n) break;
        
        if(A[ai] < B[bi]){ // matching
            ret++;
            ai++;
            bi++;
        }
        else{
            bi++;
        }
    }
    return ret;
}

int solution(vector<int> A, vector<int> B) {
    int answer = -1;
    int n = A.size();
    
    int a_min = *min_element(A.begin(), A.end());
    int a_max = *max_element(A.begin(), A.end());
    int b_min = *min_element(B.begin(), B.end());
    int b_max = *max_element(B.begin(), B.end());
    
    // n점 획득
    if(a_max < b_min) return n;
    // 0점 획득
    if(b_max < a_min) return 0;
    
    sort(A.begin(), A.end(), cmp);
    sort(B.begin(), B.end(), cmp);
    answer = set_match(A,B);
    
    return answer;
}

```