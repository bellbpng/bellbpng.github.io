---
layout: single
title:  "프로그래머스 - 이진 변환 반복하기 (Lv.2)"
excerpt: "월간 코드 챌린지 시즌1"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [이진 변환 반복하기](https://school.programmers.co.kr/learn/courses/30/lessons/70129#)

## 문제 접근
- x의 모든 0을 제거하면서 answer[1] 값을 1씩 증가시키고, 1로만 이루어진 문자열 배열을 만든다.
- 새롭게 만든 문자열 길이인 c를 구하고, 재귀 호출로 이진 변환을 하여 x를 갱신한다.
- 이를 x가 1이 될때까지 반복해야 하는데, **stoi(x)** 를 그대로 쓰면 최대 15만개의 원소를 가지는 입력 특성 상 무조건 오버플로우가 발생한다.
- 따라서, 변환된 문자열 길이 c를 이용하여 탈출 조건을 정의한다.

### 구현(All pass)
```c++
#include <string>
#include <vector>
#include <iostream>

using namespace std;

string convert_to_binary(int c){
    if(c/2 == 0) return "1";
    else return convert_to_binary(c/2) + to_string(c%2);
    
}

vector<int> solution(string s) {
    vector<int> answer(2,0); // answer[0]: 이진 변환 횟수, answer[1]: 제거된 0의 개수
    string x = s;
    int c = 0;
    while(c!=1){
        // 1. 0을 제거한다.
        string tmp;
        for(int i=0; i<x.length(); i++){
            if(x[i]=='0') answer[1]++;
            else tmp.push_back(x[i]);
        }
        
        // 2. c를 구하고 이진법으로 표현한 후 x를 갱신한다.
        c = tmp.length();
        x = convert_to_binary(c);
        answer[0]++;
    }
    
    return answer;
}

```