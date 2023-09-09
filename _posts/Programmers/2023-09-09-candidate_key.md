---
layout: single
title:  "프로그래머스 - 후보키 (Lv.2)"
excerpt: "2019 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- 👉 [후보키](https://school.programmers.co.kr/learn/courses/30/lessons/42890)

## 문제 설명
- 관계 데이터베이스에서 릴레이션의 튜플을 유일하게 식별할 수 있는 속성 또는 속성의 집합 중 다음 두 성질을 만족하는 것으 후보키라고 한다.
    - 유일성: 릴레이션에 있는 모든 튜플에 대해 유일하게 식별되어야 한다.
    - 최소성: 유일성을 가진 키를 구성하는 속성 중 하나라도 제외하는 경우 유일성이 깨지는 것을 의미한다. 즉, 릴레이션의 모든 튜플을 유일하게 식별하는 데 꼭 필요한 속성들로만 구성되어야 한다.
- 릴레이션이 주어질 때, 후보 키의 최대 개수를 구하라.

## 문제 접근
- 묶을 수 있는 속성들의 조합을 구한다.
    - 재귀호출을 통한 속성들의 집합을 찾는다.
- 위에서 찾은 속성들의 집합 혹은 속성이 유일성을 만족하는지 확인한다.
    - 조합에 포함된 속성들이 각 행의 데이터 단위가 되어, 동일한 행이 존재하는지 확인한다.
- 위에서 찾은 속성들의 잡합 혹은 속성이 최소성을 만족하는지 확인한다.
    - 속성들은 개수가 적은 것부터 후보키에 대한 조사를 하고, 후보키인 경우 `history` 배열에 넣어두어 관리해야 한다.
    - 따라서, `history` 에 담기는 속성들의 조합은 항상 유일성과 최소성을 만족한다.
- 최소성을 만족하는지 조사할 때, 조사 대상이 되는 문자열에서 `history` 의 원소들이 존재하는지를 파악하는데 substring 을 찾는 방법으로 구현했었다. 하지만, 이 경우에는 후보키가 [0,3] 이고 조사하는 집합은 [0,2,3] 일 때 최소성을 올바르게 평가하지 못하고 넘어가는 문제가 발생했다. 즉, 연속되지 않은 조합이라면 찾을 수 없는 것이었다.
- 위 로직은 반복문을 통해 직접 원소 하나하나를 비교하면서 문제를 해결할 수 있었다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <memory.h>
#include <iostream>
#include <algorithm>
using namespace std;

int answer=0, colNum=0;
vector<string> history;
bool visited[8];
vector<int> cand;

bool checkUnique(vector<vector<string>>& relation){
    vector<vector<string>> table;
    for(int r=0; r<relation.size(); r++){
        vector<string> list;
        for(int i=0; i<cand.size(); i++){
            list.push_back(relation[r][cand[i]]);
        }
        table.push_back(list);
        int tuple = 0;
        for(int i=0; i<table.size(); i++){
            int cnt=0;
            for(int j=0; j<table[i].size(); j++){
                if(list[j]==table[i][j]) cnt++;
            }
            if(cnt == list.size()) tuple++;
        }
        if(tuple>1) return false;
    }
    return true;
}

bool checkMinimality(string combi){
    for(int i=0; i<history.size(); i++){
        int cnt = 0;
        for(int j=0; j<history[i].length(); j++){
            for(int k=0; k<combi.length(); k++){
                if(history[i][j] == combi[k]) cnt++;
            }
        }
        if(cnt == history[i].length()) return false;
    }
    return true;
}

void makeCombination(vector<vector<string>>& relation, int v, int n){
    if(cand.size()==n){
        string str;
        for(int i=0; i<cand.size(); i++){
            str.push_back(cand[i] + '0');
        }
        if(checkUnique(relation) && checkMinimality(str)){
            answer++;
            history.push_back(str);
        }
        return;
    }
    
    for(int i=v; i<colNum; i++){
        if(visited[i]) continue;
        visited[i] = true;
        cand.push_back(i);
        makeCombination(relation, i+1, n);
        visited[i] = false;
        cand.pop_back();
    }
}

int solution(vector<vector<string>> relation) {
    colNum = relation[0].size();
    
    for(int i=1; i<=colNum; i++){
        memset(visited, false, sizeof(visited));
        makeCombination(relation, 0, i);
    }
    
    return answer;
}
```
