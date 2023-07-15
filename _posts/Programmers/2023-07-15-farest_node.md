---
layout: single
title:  "프로그래머스 - 가장 먼 노드 (Lv.3)"
excerpt: "그래프"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [가장 먼 노드](https://school.programmers.co.kr/learn/courses/30/lessons/49189)

## 문제 접근
- 전형적인 너비우선탐색(BFS) 문제
- 간선 데이터를 가지고 양방향 그래프를 완성한다.
- 1번 노드를 시작으로 너비우선탐색을 진행하고, 큐에 담겨질 때마다 distance를 저장해준다.
- 내림차순 정렬 후 최대 거리를 가지는 노드들의 개수를 센다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <queue>
#include <iostream>
#include <algorithm>

using namespace std;

vector<int> graph[20001];

bool cmp(int a, int b){
    return a>b;
}

void set_distance(vector<int>& distance, int n){
    vector<bool> visited(n+1, false);
    queue<int> q;
    
    int cnode = 1;
    int nnode;
    q.push(cnode);
    visited[cnode] = true;
    
    while(!q.empty()){
        cnode = q.front();
        q.pop();
        
        for(int i=0; i<graph[cnode].size(); i++){
            nnode = graph[cnode][i];
            if(visited[nnode]==true) continue;
            visited[nnode] = true;
            distance[nnode] = distance[cnode]+1;
            q.push(nnode);
        }
    }
}

int solution(int n, vector<vector<int>> edge) {
    int answer = 0;
    int len_edge = edge.size();
    
    // init
    for(int i=0; i<len_edge; i++){
        int from = edge[i][0];
        int to = edge[i][1];
        graph[from].push_back(to);
        graph[to].push_back(from);
    }
    
    vector<int> distance(n+1, 0);
    set_distance(distance, n);
    
    sort(distance.begin(), distance.end(), cmp);
    int max_val = distance[0];
    answer=1;
    for(int i=1; i<n; i++){
        if(max_val == distance[i])
            answer++;
    }
    
    return answer;
}

```