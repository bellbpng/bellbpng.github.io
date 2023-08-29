---
layout: single
title:  "프로그래머스 - 2개 이하로 다른 비트 (Lv.2)"
excerpt: "월간 코드 챌린지 시즌2"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [2개 이하로 다른 비트](https://school.programmers.co.kr/learn/courses/30/lessons/77885#)

## 문제 설명
- 양의 정수 x에 대한 함수 f(x)는 다음과 같이 정의한다.
    - x보다 크고 x와 비트가 1~2개 다른 수들중에서 제일 작은 수
- 정수들이 담긴 배열 `numbers` 가 매개변수로주어질 때, numbers의 모든 수들에 대하여 각 수의 함수 `f` 값을 배열에 차례대로 담아 return 할것

## 문제 접근1
- 실제로 이진법으로 숫자를 바꾸거나, 모든 비트를 하나하나 비교하는 경우 시간초과에 걸린다.
- 아래는 시간 초과에 걸린 두 코드

### 구현1-1(Time Exceed)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <cmath>
#include <algorithm>
using namespace std;

// 역순으로 비트 나열
string toBinary(long long x){
    if(x/2==0) return string(1, '0'+x%2);
    return string(1, '0'+x%2) + toBinary(x/2);
}

bool compareBits(string source, string cmp){
    int slen = source.length();
    int clen = cmp.length();
    int cnt = abs(slen-clen);
    if(cnt>2) return false;
    
    int len = min(slen, clen);
    for(int i=0; i<len; i++){
        if(source[i]!=cmp[i]) cnt++;
        if(cnt>2) return false;
    }
    return true;
}

vector<long long> solution(vector<long long> numbers) {
    vector<long long> answer;
    for(int i=0; i<numbers.size(); i++){
        long long num = numbers[i];
        string source = toBinary(num);
        string bits;
        long long next = num+1;
        while(true){
            bits = toBinary(next++);
            if(compareBits(source, bits)) break;
        }
        answer.push_back(next-1);
    }
    
    return answer;
}
```

### 구현1-2(Time Exceed)
```c++
#include <string>
#include <vector>

using namespace std;

bool checkBits(long long source, long long cmp){
    int cnt=0;
    long long data = source ^ cmp;
    
    while(true){ // data & 111
        int val = data & 7;
        if(val==7) cnt += 3;
        else if(val==1 || val==2 || val==4) cnt += 1;
        else if(val==3 || val==5 || val==6) cnt += 2;
        data >>= 3;
        if(cnt>2) return false;
        if(data==0) return true;
    }
}

vector<long long> solution(vector<long long> numbers) {
    vector<long long> answer;
    
    for(int i=0; i<numbers.size(); i++){
        long long num = numbers[i];
        long long next = num+1;
        if(num%2==0) {
            answer.push_back(next);
            continue;
        }
        while(true){
            if(checkBits(num, next++)) break;
        }
        answer.push_back(next-1);
    }
    
    return answer;
}
```

## 문제 접근2
- 짝수는 0번 비트가 항상 0이므로 1을 더해주면 비트가 1개만 다른 최소 숫자를 찾을 수 있다.
- 홀수는 자릿수가 작은 곳에서 큰 쪽으로 탐색하면서 0인 비트를 찾아 1로 바꾼다. 0인 비트 직전에 1인 비트를 0으로 바꾼다. (2개 비트만 다름)
    - ex) 0111 -> 1111 -> 1011

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>

using namespace std;

vector<long long> solution(vector<long long> numbers) {
    vector<long long> answer;
    
    for(int i=0; i<numbers.size(); i++){
        // 자릿수가 작은 곳부터 비트가 0인 곳을 찾는다.
        long long num = numbers[i];
        long long data = num;
        long long mask = 1;
        int cnt = 0;
        while((data & 1) != 0){
                cnt++;
                data >>= 1;
        }
        data++; // 0 -> 1
        data <<= cnt;
        data = data | num;
        if(cnt>0){
            mask <<= (cnt - 1);
            data -= mask; // 0 옆 비트를 1로, ^ 비트연산도 가능
        }
        answer.push_back(data);
    }
    return answer;
}
```
- 짝수여도 0인 비트를 1로 바꾸는 작업은 유효하다. 또, 0인 비트를 찾기 위해 비트를 이동한 횟수는 0이기 때문에 홀수에서의 로직과 동일하게 적용할 수 있다.

