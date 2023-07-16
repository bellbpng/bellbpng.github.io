---
layout: single
title:  "프로그래머스 - 예상 대진표 (Lv.2)"
excerpt: "2017 팁스타운"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [예상 대진표](https://school.programmers.co.kr/learn/courses/30/lessons/12985#)

## 문제 접근
1. n의 지수 값을 계산한다. ( **power** )
2. 배열을 절반으로 나눈다.
3. 나눈 배열에서 a와 b가 반대편에 존재한다면 power를 반환한다.
4. 서로 같은 편에 위치한다면 power를 1 감소하고, 2번으로 돌아간다.

### 구현(All Pass)
```c++
#include <iostream>

using namespace std;

int get_pow(int n){
    if(n/2==1) return 1;
    return 1+get_pow(n/2);
}

int solution(int n, int a, int b)
{
    int answer = 0;
    
    // init
    int power = get_pow(n);
    int p = 1, r = n;
    int q = (p+r)/2;
    
    int big = a>b ? a : b;
    int small = a<b ? a : b;
    
    while(true){
        if(small>=p && small<=q && big>=q+1 && big<=r){ // 서로 반대편에 있는 경우
            answer = power;
            break;
        }
        else if(small<=q) { // 왼쪽에 있는 경우
            r = q;
        }
        else if(small>=q+1) { // 오른쪽에 있는 경우
            p = q+1;
        }
        power--;
        q = (p+r)/2;
    }

    return answer;
}

```
- 문제의 핵심은 탑다운 방식으로 크게 접근해서 작게 쪼개가는 것
