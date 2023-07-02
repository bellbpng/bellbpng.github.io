---
layout: single
title:  "프로그래머스 - 연속 펄스 부분 수열의 합(Lv.3)"
excerpt: "연습문제"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제풀이1 (실패, 시간초과)
- 펄스 수열이 적용된 각 원소는 1이 곱해지느냐 -1이 곱해지느냐로 나뉜다.
- 수열의 첫 원소에 1이 곱해지는 경우와 -1이 곱해지는 경우 두 가지로 펄스 수열을 좁힐 수 있다.
- 두 펄스 수열의 부분 연속 배열을 찾아서 그 합이 최대가 되는 경우를 찾으면 된다.
- 부분합으로 접근하였고, 투 포인터를 이용하였으나 시간초과, 실패(segmentation fault)로 통과하지 못했다.

### 구현
```c++
#include <string>
#include <vector>
#include <iostream>

using namespace std;

// Debugging
void print_vector(vector<long long> v){
    for(auto ele:v){
        cout << ele << " ";
    }
    cout << endl;
}

void get_pulse_array(vector<int>& sequence, vector<int>& pulse1, vector<int>& pulse2){
    int n=sequence.size();
    for(int i=0; i<n-1; i+=2){
        pulse1.push_back(sequence[i]*1);
        pulse1.push_back(sequence[i+1]*(-1));
        pulse2.push_back(sequence[i]*(-1));
        pulse2.push_back(sequence[i+1]*1);
    }
}

// 부분합을 구한다. p1_sum[2] = p1_sum[0] + p1_sum[1] + p1_sum[2]
void get_partial_sum(vector<int>& pulse1, vector<int>& pulse2, vector<long long>& p1_sum, vector<long long>& p2_sum){
    int n = pulse1.size();
    int sum1 = 0, sum2 = 0;
    for(int i=0; i<n; i++){
        sum1 += pulse1[i];
        p1_sum.push_back(sum1);
        sum2 += pulse2[i];
        p2_sum.push_back(sum2);
    }
}

long long get_max_pulse(vector<long long>& p1_sum, vector<long long>& p2_sum){
    int max = p1_sum[0];
    int n = p1_sum.size();
    int start, end;
    long long val1, val2, tmp;
    
    for(int len=n; len>=1; len--){
        int diff = n-len;
        for(int i=0; i<=diff; i++){
            start = i;
            end = (n-1) - (diff - i);
            if(start>0){
                val1 = p1_sum[end] - p1_sum[start-1];
                val2 = p2_sum[end] - p2_sum[start-1];
            }
            else{
                val1 = p1_sum[end];
                val2 = p2_sum[end];
            }
            tmp = val1>val2 ? val1 : val2;
            if(max<tmp) max = tmp;
        }
    }
    return max;
}

long long solution(vector<int> sequence) {
    long long answer = 0;
    
    vector<int> pulse1, pulse2;
    get_pulse_array(sequence, pulse1, pulse2);
    
    vector<long long> p1_sum, p2_sum;
    get_partial_sum(pulse1, pulse2, p1_sum, p2_sum);
    
    answer = get_max_pulse(p1_sum, p2_sum);
    
    return answer;
}
```

#### 개선한 점
- 동적 계획법(Dynamic Programming)을 이용해 시간초과 문제를 해결할 수 있었다.
- 연속 부분 배열을 어느 시점에서 끊어내는지가 문제 풀이의 핵심이 되는 부분이다.
- 탐색을 하는 원소가 이전까지 탐색된 배열들과 합쳐졌을 때, 값이 더 작아진다면 해당 연속 부분 배열을 유지할 필요가 없다.
- 즉, dp[i] = max(dp[i-1]+arr[i], arr[i]) 라는 수식을 따를 수 있다.


### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

void get_pulse_array(vector<int>& sequence, vector<long long>& pulse1, vector<long long>& pulse2){
    int n=sequence.size();
    for(int i=0; i<n; i++){
        if(i%2==0){
            pulse1.push_back(sequence[i]);
            pulse2.push_back(sequence[i]*(-1));
        }
        else{
            pulse1.push_back(sequence[i]*(-1));
            pulse2.push_back(sequence[i]);
        }
    }
}

long long get_max_pulse(vector<long long>& pulse1, vector<long long>& pulse2){
    int n = pulse1.size();
    vector<long long> dp1(n+1,0), dp2(n+1,0);
    long long ret = -99999999;
    for(int i=1; i<=n; i++){
        dp1[i] = max(dp1[i-1]+(long long)pulse1[i-1], (long long)pulse1[i-1]);
        dp2[i] = max(dp2[i-1]+(long long)pulse2[i-1], (long long)pulse2[i-1]);
        ret = max(ret, dp1[i]);
        ret = max(ret, dp2[i]);
    }

    return ret;
}

long long solution(vector<int> sequence) {
    long long answer = 0;
    
    vector<long long> pulse1, pulse2;
    get_pulse_array(sequence, pulse1, pulse2);
    
    if(sequence.size()==1) // 길이가 1인 경우
        answer = max(pulse1[0], pulse2[0]);
    else
        answer = get_max_pulse(pulse1, pulse2);
    
    
    return answer;
}

```