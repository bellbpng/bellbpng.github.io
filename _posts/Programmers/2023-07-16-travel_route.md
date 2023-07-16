---
layout: single
title:  "프로그래머스 - 여행경로 (Lv.3)"
excerpt: "깊이/너비 우선 탐색(DFS/BFS)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [여행경로](https://school.programmers.co.kr/learn/courses/30/lessons/43164)

## 문제 접근
- 항공권의 출발 공항과 도착 공항 정보를 이용해 재귀 호출을 이용한 완전탐색을 한다.
- 항공권을 알파벳 순서로 정렬한다.
- 정렬된 항공권을 탐색하기 때문에 가장 처음에 완성된 루트가 정답이 되어야 한다.
- 따라서, **flag** 변수를 두어 루트가 한번 완성되면 방문표시를 해제하지 않고, 탐색을 최대한 빨리 종료할 수 있도록 한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

bool flag = false;

// 오름차순 정렬
bool cmp(vector<string> v1, vector<string> v2){
    if(v1[0]!=v2[0]) return v1[0] < v2[0];
    else return v1[1] < v2[1];
}

void set_route(vector<vector<string>>& tickets, vector<bool>& visited, vector<string>& answer, string airport, int cnt){
    // 기저사례: 모든 티켓을 방문한 경우
    if(cnt==tickets.size()){
        flag = true;
        answer.push_back(airport); // 마지막 도착지
        return;
    }
    
    answer.push_back(airport);
    for(int i=0; i<tickets.size(); i++){
        if(!visited[i] && tickets[i][0]==airport) { // 티켓 고르기
            visited[i] = true;
            set_route(tickets, visited, answer, tickets[i][1], cnt+1);
            if(flag==false){ // 모든 티켓을 방문하지 못한 경우: 루트를 찾기 위해 방문 표시를 해제함
                answer.pop_back();
                visited[i]=false;
            }
        }
    }
}

vector<string> solution(vector<vector<string>> tickets) {
    vector<string> answer;
    
    // init
    sort(tickets.begin(), tickets.end(), cmp);
    
    // dfs
    vector<bool> visited(tickets.size(), false);
    set_route(tickets, visited, answer, "ICN", 0);
    return answer;
}

```
