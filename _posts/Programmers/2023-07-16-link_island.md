---
layout: single
title:  "프로그래머스 - 섬 연결하기 (Lv.3)"
excerpt: "탐욕법(Greedy), 크루스칼 알고리즘, 최소신장트리, 서로소 집합"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [섬 연결하기](https://school.programmers.co.kr/learn/courses/30/lessons/42861)

## 문제 접근
- 최소신장트리(MST)에 대해서 알고 있어야 한다.
- 사이클이 없는 그래프 구조로, 간선의 합이 최소가 되는 트리를 말한다.
- 가중치를 오름차순으로 정렬하고, 두 노드를 연결할지 말지를 선택한다.
- 선택의 기준은 해당 노드가 서로 같은 그룹(루트 노드를 공유하는지)에 속하는지로 결정한다.
- 서로 같은 그룹에 속하지 않는다면 연결한 후 그룹을 합친다.

### 구현(Fail)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

bool cmp(vector<int> a, vector<int> b){
    return a[2] < b[2];
}

// 크루스칼 알고리즘
// 최소신장트리 - 사이클이 없는 그래프, 가중치 합이 최소가 되는 트리
int solution(int n, vector<vector<int>> costs) {
    int answer = 0;
    
    // 오름차순 정렬
    sort(costs.begin(), costs.end(), cmp);
    vector<int> group(n); // 노드 별 그룹(루트) 번호. group[i] = 1 은 i번 노드는 1번 노드를 루트로 가짐
    for(int i=0; i<n; i++)
        group[i] = i;
    
    int from, to, w;
    for(int i=0; i<costs.size(); i++){
        from = costs[i][0];
        to = costs[i][1];
        w = costs[i][2];
        
        if(group[from] != group[to]){ // 루트가 다르면 연결하고, 작은 번호가 루트가 되도록 한다.
            answer += w;
            if(group[from] < group[to])
                group[to] = group[from];
            else
                group[from] = group[to];
        }
    }
    
    return answer;
}

```
- 실패 원인은 노드 간의 연결 관계가 루트 노드가 작은 번호가 들어오는 순간 해당 번호로 비교된 노드의 루트번호를 바꿔버리기 때문에 간선 관계가 엉망이 된다.

#### 개선한 점
- 노드 번호와 그룹 번호를 구분해서 생각할 필요가 있다. 즉, 노드 번호 != 그룹 번호.
- 혼란스러웠던 점은 항상 작은 번호를 루트 번호로 여겼기 때문에 마치 작은 번호가 항상 루트 쪽에 위치한다는 생각을 했었다.
- 하지만 실제로 루트에 위치하는 노드는 노드 간 연결 상태에 따라 달라질 수 있고, 연결하는데 비용이 큰 노드일수록 아래쪽에 위치하게 된다.
- 따라서 루트 번호라는 말보다는 그룹 번호라고 생각하는게 편하고, 0번부터 그룹 번호가 매겨진다. 

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

bool cmp(vector<int> a, vector<int> b){
    return a[2] < b[2];
}

int find_group(vector<int>& group, int node){
    if(node == group[node])
        return node;
    else
        return find_group(group, group[node]);
}

// 크루스칼 알고리즘
// 최소신장트리 - 사이클이 없는 그래프, 가중치 합이 최소가 되는 트리
int solution(int n, vector<vector<int>> costs) {
    int answer = 0;
    
    // 오름차순 정렬
    sort(costs.begin(), costs.end(), cmp);
    vector<int> group(n); // 노드 별 그룹(루트) 번호. group[i] = 1 은 i번 노드는 1번 노드를 루트로 가짐
    for(int i=0; i<n; i++)
        group[i] = i;
    
    int from, to, w;
    for(int i=0; i<costs.size(); i++){
        from = costs[i][0];
        to = costs[i][1];
        w = costs[i][2];
        
        int gfrom = find_group(group, from);
        int gto = find_group(group, to);
        if(gfrom != gto){
            answer += w;
            if(gfrom<gto) group[gto] = gfrom;
            else group[gfrom] = gto;
        }
    }
    
    return answer;
}

```
- **group[gto] = gfrom** , **group[gfrom] = gto** 부분을 이해를 못했었는데, 이 부분은 하나의 그룹의 최상위 노드의 그룹 번호를 바꿔주는 부분으로, 해당 노드의 하위 노드들도 함께 그룹을 옮기는 역할을 한다.


