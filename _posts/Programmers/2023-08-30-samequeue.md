---
layout: single
title:  "프로그래머스 - 두 큐 합 같게 만들기 (Lv.2)"
excerpt: "2022 KAKAO TECH INTERNSHIP"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [두 큐 합 같게 만들기](https://school.programmers.co.kr/learn/courses/30/lessons/118667)

## 문제 설명
- 길이가 같은 두 개의 큐가 주어진다. 하나의 큐를 골라 원소를 추출(pop)하고, 추출된 원소를 다른 큐에 집어넣는(insert) 작업을 통해 각 큐의 원소 합이 같도록 만든다.
- 한 번의 pop과 한 번의 insert를 합쳐서 작업을 1회 수행한 것으로 간주할 때, 최소 작업 횟수를 구하라.
- 어떻게 해도 두 큐를 같게 만들 수 없는 경우 -1을 반환하라.

## 문제 접근
- 두 큐에 포함된 전체 원소의 합이 홀수인 경우 -1을 반환한다.
- 큐의 원소의 합이 큰 쪽 -> 작은 쪽으로 원소를 pop, insert 해주면 된다.
- 탐색을 종료하는 시점을 정해준다. 처음 두 큐의 길이를 n이라고 할때, 각각의 큐에 대하여 pop 을 한 횟수가 2xn 이 된다면 본래 배열의 형태와 같은 모습을 하게 된다. 따라서, 하나의 큐에서 2xn 번 pop 연산을 하게 되는 경우 -1을 반환한다.
    - 하나의 큐에서 원소가 비어지는 경우도 탐색을 종료해야하는 조건이라고 생각하였으나, 한개의 원소가 전체 배열의 합을 크게 좌우하는 경우 배열이 비어지더라도 탐색을 계속 진행해야 한다.
- 재귀적으로 구현.

### 구현(All pass)
```c++
#include <string>
#include <vector>
#include <queue>
#include <algorithm>
#include <iostream>
using namespace std;

int ans = 0;
int popCnt1 = 0, popCnt2 = 0;
int n;
void doLogic(queue<long long>& q1, queue<long long>& q2, long long sum1, long long sum2, int cnt){
    // 원래 자신으로 돌아온 경우
    if(popCnt1==2*n || popCnt2==2*n){
        ans=-1;
        return;
    }
    
    // base case: 두 수 합이 같은 경우
    if(sum1 == sum2) {
        ans=cnt;
        return;
    }
    else if(sum1 > sum2){
        q2.push(q1.front());
        sum1 -= q1.front();
        sum2 += q1.front();
        q1.pop();
        popCnt1++;
    }
    else if(sum1 < sum2){
        q1.push(q2.front());
        sum2 -= q2.front();
        sum1 += q2.front();
        q2.pop();
        popCnt2++;
    }
    doLogic(q1, q2, sum1, sum2, cnt+1);
}



int solution(vector<int> queue1, vector<int> queue2) {
    queue<long long> q1, q2;
    long long sum = 0, sum1 = 0, sum2 = 0;;
    n = queue1.size()*2;
    for(int i=0; i<queue1.size(); i++){
        q1.push((long long)queue1[i]);
        q2.push((long long)queue2[i]);
        sum1 += (long long)queue1[i];
        sum2 += (long long)queue2[i];
    }
    sum = sum1+sum2;
    // 합이 홀수인 경우 두 큐의 원소를 같게 만들 수 없음
    if(sum%2==1) return -1;
    doLogic(q1, q2, sum1, sum2, 0);
    
    return ans;
}
```
