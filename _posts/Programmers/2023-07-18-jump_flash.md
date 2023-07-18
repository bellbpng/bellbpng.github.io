---
layout: single
title:  "프로그래머스 - 점프와 순간 이동 (Lv.2)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [점프와 순간 이동](https://school.programmers.co.kr/learn/courses/30/lessons/12980)

## 문제 접근
- n을 어떻게 만들까? 로 접근하는 것이 중요하다.
- n으로 갈 때 건전지 사용량이 최소가 되는 방법은 **(이전의 위치) x 2 = n**  이 되는 경우이다.
- 따라서, n이 짝수라면 2로 나누고 n이 홀수라면 1을 뺀 후 건전지 사용량을 추가한다.
- 재귀 호출을 통해 구현한다.

### 구현(All Pass)
```c++
#include <iostream>
using namespace std;

void get_min_energy(int n, int& ans){
    if(n==0) return;
    if(n%2==0) get_min_energy(n/2, ans);
    else {
        n--; ans++;
        get_min_energy(n/2, ans);
    }
}

int solution(int n)
{
    int ans = 0;
    get_min_energy(n, ans);

    return ans;
}

```
