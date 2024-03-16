---
layout: single
title:  "프로그래머스 - 짝지어 제거하기 (Lv.2)"
excerpt: "2017 팁스타운"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [짝지어 제거하기](https://school.programmers.co.kr/learn/courses/30/lessons/12973)

## 문제 접근
- 최대 문자열 길이기 백만이라는 것을 고려했을 때, 시간복잡도를 최소화 해야 한다.
- 문자열의 인덱스를 가리키는 front, rear 변수를 제어하며 짝지어진 문자열을 삭제하는 작업을 한다.
- 이때, 중간에 문자열이 제거되면 앞부분과 뒷부분이 합쳐지게 되기 때문에 앞에서 부터 제거되지 못한 문자열은 스택 자료구조로 담아둔다.
    - 최근에 제거되지 못한 문자와 rear의 문자가 짝을 짓는지 확인해야 하기 떄문에 LIFO 구조가 필요하다.

### 구현1(All Pass)
```c++
#include <iostream>
#include <string>
#include <vector>
#include <stack>
using namespace std;

int solution(string s)
{
    int answer = -1;
    int len = s.length();
    int front = 0, rear = front+1;
    stack<int> not_removed;
    
    while(true){
        if(front>=len){
            answer = 1;
            break;
        }
        if(rear>=len){
            answer = 0;
            break;
        }
        
        if(s[front]==s[rear]){
            rear++;
            if(not_removed.size()==0)
                front = rear++;
            else{
                front = not_removed.top();
                not_removed.pop();
            }
        }
        else{
            not_removed.push(front);
            front = rear++;
        }
    }
    

    return answer;
}

```

### 구현2
```c++
#include <iostream>
#include <string>
using namespace std;

int solution(string s)
{
    string nstr;
    for(int i=0; i<s.length(); i++){
        if(nstr.length()==0) nstr.push_back(s[i]);
        else if(nstr.back() == s[i]) nstr.pop_back();
        else if(nstr.back() != s[i]) nstr.push_back(s[i]);
    }
    if(nstr.length()==0) return 1;
    else return 0;
}
```