---
layout: single
title:  "프로그래머스 - [3차] n진수 게임 (Lv.2)"
excerpt: "2018 KAKAO BLIND RECRUITMEMT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [n진수 게임](https://school.programmers.co.kr/learn/courses/30/lessons/17687)

## 문제 접근
- 0부터 차례대로 n진수로 변환한 문자열의 길이를 튜브가 t번 말할 수 있을 때까지 숫자를 증가시키며 n진수 변환을 해야 한다.
- 게임에 참가한 인원 m명이 모두 t번씩 말할 수 있을 길이로 n진수 문자열 변환 길이를 제한할 수 있으므로, **len >= m * t** 를 만족할 때까지 n진수 변환을 한다.
- 이때, 0은 항상 0으로 표현되고, 16진수까지 변환하므로 나머지가 10이 넘어가는 경우 A,B,C,.. 로 표현해야 한다.
- 변환된 n진수 문자열에서 튜브가 말하게 되는 인덱스를 찾으면 되고, 공식을 유도하면 **index = p-1 + p*i (i>=0)** 을 만족한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>

using namespace std;

string ConvToNexp(int n, int num){
    if(num == 0) return string(1, '0');
    if(num/n == 0) {
        int r = num%n;
        if(r>=10){
            r = 65 + (r-10);
            return string(1, r);
        }
        return to_string(r);
    }
    
    int r = num%n;
    if(r >= 10){
        r = 65 + (r - 10);
        return ConvToNexp(n, num/n) + string(1, r);
    }
    return ConvToNexp(n, num/n) + to_string(r);
}

void GetMathString(string& str, int len, int n, int num){
    if(str.size()>=len) return;
    
    str = str + ConvToNexp(n, num);
    GetMathString(str, len, n, num+1);
}

string solution(int n, int t, int m, int p) {
    string answer = "";
    int len = m * t;
    string str;
    GetMathString(str, len, n, 0);
    // cout << str << endl;
    
    for(int i=0; i<t; i++){
        answer.push_back(str[p-1 + m*i]);
    }
    
    return answer;
}

```
- 문자열로 변환하기 위해 사용하는 **string(n, char)** 은 char 문자를 n 개 붙여서 반환하는 함수이다. 따라서 char 자리에는 반드시 **붙일 문자** 가 와야한다.
- **to_string(int)** 함수는 int 정수를 문자열로 변환하는 함수이다. 즉, 숫자를 문자로 표현한다. 

