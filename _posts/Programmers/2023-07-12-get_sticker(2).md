---
layout: single
title:  "프로그래머스 - 스티커 모으기2 (Lv.3)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [스티커 모으기(2)](https://school.programmers.co.kr/learn/courses/30/lessons/12971)

### 구현(All Pass)
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 첫번째 스티커를 뜯은 경우
int remove_first_sticker(vector<int>& sticker){
    int n = sticker.size();
    vector<int> dp(n,0);
    dp[0] = sticker[0];
    dp[1] = dp[0];
    
    // i번째 스티커를 뜯은 경우와 뜯지 않은 경우 중 큰 값을 선택
    for(int i=2; i<n; i++){
        dp[i] = max(sticker[i]+dp[i-2], dp[i-1]);
    }
    return dp[n-2]; // 첫번째를 뜯었기 때문에 n-2가 최대
}

// 첫번째 스티커를 뜯지 않은 경우
int not_remove_first_sticker(vector<int>& sticker){
    int n = sticker.size();
    vector<int> dp(n,0);
    dp[0] = 0;
    dp[1] = sticker[1];
    
    for(int i=2; i<n; i++){
        dp[i] = max(sticker[i]+dp[i-2], dp[i-1]);
    }
    return dp[n-1]; 
}

int solution(vector<int> sticker)
{
    int answer =0;
    
    if(sticker.size()==1) 
        answer = sticker[0];
    else
        answer = max(remove_first_sticker(sticker), not_remove_first_sticker(sticker));

    return answer;
}

```
- 처음엔 재귀 탐색을 생각하였으나,재귀 탈출 조건을 세우는 것이 까다롭고 경우의 수를 따지는 과정이 복잡했다.
- 배열의 길이가 10만까지다 보니 완전 탐색은 시간초과에 걸릴 수 있다고 생각했다.
- 동적계획법을 떠올려도 알고리즘을 추상화 하는데 어려움이 있었지만, 핵심은 첫번째 스티커를 떼느냐 떼지 않느냐 였다.
- 첫번째 스티커를 떼는 경우는 n-1번째 스티커는 뗄 수 없기 때문에 dp[n-2]가 가능한 최댓값이 된다.
- 첫번째 스티커를 떼지 않는 경우는 dp[n-1]이 가능한 최댓값이 된다.
- 위 두가지 경우 중 큰 값을 찾는다.
    - dp[i]는 0번~i번째 스티커를 떼는 과정에서 합이 최대인 값을 담는다.
    - dp[i]는 i번째 스티커를 떼거나 떼지 않거나로 계산된다.
        - 스티커를 떼는 경우: dp[i] = sticker[i] + dp[i-2]
        - 스티커를 떼지 않는 경우: dp[i] = dp[i-1]





