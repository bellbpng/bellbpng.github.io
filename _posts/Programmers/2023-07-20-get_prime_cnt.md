---
layout: single
title:  "프로그래머스 - k진수에서 소수 개수 구하기 (Lv.2)"
excerpt: "2022 KAKAO BLIND RECRUITMEMT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [k진수에서 소수 개수 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/92335)

## 문제 접근
- 재귀호출을 이용하여 k진수로 변환한다. 양의 정수 n은 최대 백만까지 주어지므로 2진수의 경우 길이가 매우 길어질 수 있다.
- 숫자가 연속으로 존재하는 문자열을 파싱하여 배열에 담는다. 위에서 언급한 이유로 **long long** 자료형을 사용해야 한다.
- 소수인지 판별하는 함수는 에라토스테네스의 체 알고리즘을 사용할 경우 메모리 초과에 걸린다. 시간복잡도가 높아졌지만 이중반복문으로 소수를 판별해도 문제를 통과할 수 있었다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>
#include <cmath>

using namespace std;

// k진수 변환
string GetExpressK(int n, int k){
    if(n/k==0) return to_string(n%k);
    else return GetExpressK(n/k, k) + to_string(n%k);
}

bool check_prime(long long n){
    if(n==1) return false;
    if(n==2) return true;
    if(n%2==0) return false;
    
    for(int i=3; i<=sqrt(n); i+=2){
        if(n%i==0) return false;
    }
    return true;
}

int solution(int n, int k) {
    int answer = 0;
    
    string numStrK = GetExpressK(n,k);
    int len = numStrK.length();
    string str;
    vector<long long> cand;
    for(int i=0; i<len; i++){
        if(numStrK[i]!='0'){
            str.push_back(numStrK[i]);
        }
        else if(numStrK[i]=='0'){
            if(str.size()>0){
                cand.push_back(stoll(str));
                str.clear();
            }
        }
        
        if(i==len-1){
            if(str.size()>0){
                cand.push_back(stoll(str));
                str.clear();
            }
        }
    }
    
    for(auto ele:cand){
        if(check_prime(ele)) answer++;
    }
    
    return answer;
}
```
