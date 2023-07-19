---
layout: single
title:  "프로그래머스 - 소수 만들기 (Lv.1)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [소수 만들기](https://school.programmers.co.kr/learn/courses/30/lessons/12977)

## 문제 접근
- **에라토스테네스의 체** 를 활용하여 소수를 판별할 수 있도록 한다.
    - 앞에서부터 소수의 배수는 모두 지우는 방법
- 재귀호출을 이용해 3개의 숫자로 순열이 만들어지면, 소수인지 확인한다.

### 구현(All Pass)
```c++
#include <vector>
#include <iostream>
using namespace std;

vector<int> primes(3000, 1);

int get_prime_combination(vector<int>& nums, vector<bool>& visited, vector<int>& v, int node, int n){
    if(v.size()==3){
        int num = v[0] + v[1] + v[2];
        if(primes[num]==1) return 1;
        else return 0;
    }
    
    int ret = 0;
    for(int i=node; i<n; i++){
        if(visited[i]==true) continue;
        visited[i] = true;
        v.push_back(nums[i]);
        ret += get_prime_combination(nums, visited, v, i+1, n);
        visited[i] = false;
        v.pop_back();
    }
    return ret;
}

int solution(vector<int> nums) {
    int answer = -1;
    // init
    int n = nums.size();
    for(int i=2; i<=1500; i++){
        if(primes[i]==0) continue;
        for(int j=i+i; j<=3000; j+=i)
            primes[j] = 0;
    }
    vector<bool> visited(n, false);
    vector<int> v;
    answer = get_prime_combination(nums, visited, v, 0, n);
    
    return answer;
}

```

