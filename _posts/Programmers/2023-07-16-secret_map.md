---
layout: single
title:  "프로그래머스 - 1차 비밀지도 (Lv.1)"
excerpt: "2018 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [1차 비밀지도](https://school.programmers.co.kr/learn/courses/30/lessons/17681)

## 문제 접근
- 정수 배열에 들어 있는 원소를 2진수 문자열로 표현하는 과정에서 길이를 반드시 n에 맞춰야 한다.
    - 이진수 변환 과정에서 0이 들어가는 경우도 고려해야 한다.
- 길이가 짧은 경우 앞에 '0'을 붙인다.
- 변환된 두 이진수 배열을 비교하여 둘다 '0'인 경우에만 공백 문자를 넣고, 이외의 경우는 # 문자를 넣는다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>

using namespace std;

string get_to_bin(int n){
    if(n==0) return string("0"); // n이 0인 경우
    if(n/2==0) return string("1");
    else return get_to_bin(n/2) + to_string(n%2);
}

void set_size(string& s1, string& s2, int n){
    string tmp;
    if(s1.size()<n){
        for(int i=0; i<n-s1.size(); i++)
            tmp.push_back('0');
        s1 = tmp + s1;
    }
    tmp.clear();
    if(s2.size()<n){
        for(int i=0; i<n-s2.size(); i++)
            tmp.push_back('0');
        s2 = tmp + s2;
    }
}

vector<string> solution(int n, vector<int> arr1, vector<int> arr2) {
    vector<string> answer;
    
    for(int i=0; i<n; i++){
        string s1 = get_to_bin(arr1[i]);
        string s2 = get_to_bin(arr2[i]);
        set_size(s1, s2, n);
        
        string decoder;
        for(int j=0; j<n; j++){
            if(s1[j]=='0' && s2[j]=='0')
                decoder.push_back(' ');
            else
                decoder.push_back('#');
        }
        answer.push_back(decoder);
    }
    
    return answer;
}

```