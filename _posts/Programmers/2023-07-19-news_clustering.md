---
layout: single
title:  "프로그래머스 - [1차] 뉴스 클러스터링 (Lv.2)"
excerpt: "2018 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [뉴스 클러스터링](https://school.programmers.co.kr/learn/courses/30/lessons/17677)

## 문제 접근
- 주어진 두 문자열의 알파벳을 소문자로 바꾸고, 다중집합을 만든다.
    - 알파벳이 아닌 문자는 무시하므로, 알파벳으로 문자가 2개 채워졌는지 확인을 해줘야 한다.
- 교집합을 이루는 원소의 개수와 합집합을 이루는 원소의 개수를 구한다.
    - 길이가 짧은 집합과 길이가 긴 집합으로 나누어 탐색한다.
    - 길이가 짧은 집합의 원소가 길이가 긴 집합의 원소에 포함되는지 확인한다. 이때, 원소가 중복되는 경우가 있으므로 방문표시를 확인한다.
    - 합집합 원소의 개수는 위 과정에서 구한 교집합 원소의 개수에서 추가적인 탐색이 필요하다.
    - 교집합 탐색을 하면서 두 집합의 원소들 중 방문표시가 되지 않은 원소들을 합집합 원소의 개수에 더한다.

### 구현(All Pass)
```c++
#include <string>
#include <algorithm>
#include <iostream>
#include <cctype>

using namespace std;

vector<string> init_set(string str){
    vector<string> str_set;
    string tmp;
    for(int i=0; i<str.size(); i++){
        if(isalpha(str[i])){
            if(isupper(str[i]))
                tmp.push_back(tolower(str[i]));
            else
                tmp.push_back(str[i]);
        }
       if(isalpha(str[i+1])){
            if(isupper(str[i+1]))
                tmp.push_back(tolower(str[i+1]));
            else
                tmp.push_back(str[i+1]);
        }
        if(tmp.size()==2)
            str_set.push_back(tmp);
        tmp.clear();
    }
    return str_set;
}

float get_jacard(vector<string>& short_set, vector<string>& long_set){
    int inter_count = 0;
    int union_count = 0;
    float ret = 0.0f;
    int long_len = long_set.size();
    int short_len = short_set.size();
    vector<int> checked(long_len, 0);
    vector<int> checked_s(short_len, 0);
    
    string ref;
    for(int i=0; i<short_set.size(); i++){
        ref = short_set[i];
        for(int j=0; j<long_set.size(); j++){
            if(ref==long_set[j] && checked[j]==0){
                inter_count++;
                checked[j]=1;
                checked_s[i]=1;
                break;
            }
        }
    }
    
    union_count = inter_count;
    for(int i=0; i<short_set.size(); i++){
        if(checked_s[i]==0)
            union_count++;
    }
    for(int i=0; i<long_set.size(); i++){
        if(checked[i]==0)
            union_count++;
    }
    ret = (float)inter_count / union_count;
    
    return ret;
}


int solution(string str1, string str2) {
    int answer = 0;
    // init
    vector<string> str1_set = init_set(str1);
    vector<string> str2_set = init_set(str2);   
    vector<string> short_set = str1_set < str2_set ? str1_set : str2_set;
    vector<string> long_set = str1_set > str2_set ? str1_set : str2_set;
    
    // A=B
    if(short_set == long_set) return 65536;
    
    float jacard = get_jacard(short_set, long_set);
    answer = int(jacard * 65536);
    
    return answer;
}
```
