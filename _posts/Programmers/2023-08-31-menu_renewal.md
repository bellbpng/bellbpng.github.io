---
layout: single
title:  "프로그래머스 - 메뉴 리뉴얼 (Lv.2)"
excerpt: "2021 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [메뉴 리뉴얼](https://school.programmers.co.kr/learn/courses/30/lessons/72411)

## 문제 설명
- 손님들이 주문한 단품메뉴 조합이 문자열의 형태로 주어진다.
    - 1번 손님은 ABCFG, 2번 손님은 AC, 3번 손님은 ACEGF .. 와 같은 모양으로 주어짐
- 각 손님들의 메뉴조합을 보고 최소 2명 이상의 손님으로부터 동일한 패턴의 조합이 나온다면 코스요리로 만든다.
- 코스요리로 만들고 싶은 단품메뉴들의 개수가 배열 `course` 에 주어질 때, 하나의 배열에 단품메뉴 조합들을 담아서 반환하라.

## 문제 접근
- 단품메뉴 조합들을 오름차순으로 반환해야 하므로, `orders` 배열의 각 원소를 오름차순 정렬한다.
- 코스요리로 만들고 싶은 단품메뉴들의 개수보다 길이가 작은 조합에서는 조합을 찾는 재귀호출을 하지 않는다.
- `course` 에 담긴 단품메뉴들의 개수별로 아래와 같은 로직을 반복한다.
    - `orders` 에 담긴 손님별 단품메뉴들의 조합에서 `course[i]` 의 길이에 맞는 패턴을 찾는다. 찾은 패턴은 map 자료구조에 담아둔다.
    - 등장횟수가 가장 많았던 조합을 찾는다. 단, 등장횟수는 2이상이어야 한다.
    - 가장 많이 등장한 조합이 여러가지라면 모두 정답 배열에 담는다.
- 정답 배열에 담긴 문자열을 오름차순으로 정렬하여 반환한다.

### 구현(ver1)
```c++
#include <string>
#include <vector>
#include <map>
#include <algorithm>
#include <queue>
#include <iostream>

using namespace std;

bool cmp(pair<string, int> p1, pair<string, int> p2){
    return p1.second > p2.second;    
}

void findCombo(map<string, int>& dict, int v, string str, string menu, int length){
    if(menu.size()==length){
        // cout << menu << endl;
        dict[menu]++;
        return;
    }
    
    for(int i=v; i<str.size(); i++){
        menu.push_back(str[i]);
        findCombo(dict, i+1, str, menu, length);
        menu.pop_back();
    }
}

vector<string> solution(vector<string> orders, vector<int> course) {
    vector<string> answer;
    vector<int> count(11, 0); // 메뉴 길이별로 몇개 있는지 확인
    for(int i=0; i<orders.size(); i++){
        sort(orders[i].begin(), orders[i].end());
        int len = orders[i].length();
        for(int j=1; j<=len; j++){
            count[j]++;
        }
    }
    
    map<string, int> dict;
    for(int i=0; i<course.size(); i++){
        int comboNum = course[i];
        if(count[comboNum]<2) continue;
        for(int j=0; j<orders.size(); j++){
            if(orders[j].length() < comboNum) continue;
            findCombo(dict, 0, orders[j], "", comboNum);
        }
    }
    
    vector<pair<string, int>> cand[11];
    for(auto ele:dict){
        int len = ele.first.length();
        if(ele.second<2) continue;
        cand[len].push_back(make_pair(ele.first, ele.second));
    }
    for(int i=0; i<course.size(); i++){
        sort(cand[course[i]].begin(), cand[course[i]].end(), cmp);
    }
    
    // set answer
    for(int i=0; i<course.size(); i++){
        int len = course[i];
        if(cand[len].size()==0) continue;
        int max = cand[len][0].second;
        answer.push_back(cand[len][0].first);
        for(int j=1; j<cand[len].size(); j++){
            if(max == cand[len][j].second)
                answer.push_back(cand[len][j].first);
            else break;
        }
    }
    
    sort(answer.begin(), answer.end());
    return answer;
}
```
- 불필요한 메모리와 정렬, 연산 과정이 많다.
- 만들고자 하는 단품메뉴들의 개수별로 조합을 찾고, 최대 등장횟수를 구하면 되는데 모든 개수에 대해서 조합을 찾은 후에 개수별로 정렬하겠다는 생각이 코드 구현을 복잡하게 만들었다.

### 구현(ver2. updated)
```c++
#include <string>
#include <vector>
#include <map>
#include <algorithm>

using namespace std;

void findCombo(map<string, int>& dict, int v, string str, string menu, int length){
    if(menu.size()==length){
        dict[menu]++;
        return;
    }
    
    for(int i=v; i<str.size(); i++){
        menu.push_back(str[i]);
        findCombo(dict, i+1, str, menu, length);
        menu.pop_back();
    }
}

vector<string> solution(vector<string> orders, vector<int> course) {
    vector<string> answer;
    for(int i=0; i<orders.size(); i++){
        sort(orders[i].begin(), orders[i].end());
    }
    
    
    for(auto comboNum:course){
        map<string, int> dict;
        for(int j=0; j<orders.size(); j++){
            if(orders[j].length() < comboNum) continue;
            findCombo(dict, 0, orders[j], "", comboNum);
        }
        
        int mcnt = 0;
        for(auto ele:dict){
            mcnt = max(mcnt, ele.second);
        }
        
        for(auto ele:dict){
            if(ele.second == mcnt && mcnt>=2)
                answer.push_back(ele.first);
        }
    }
    
    sort(answer.begin(), answer.end());
    return answer;
}
```
