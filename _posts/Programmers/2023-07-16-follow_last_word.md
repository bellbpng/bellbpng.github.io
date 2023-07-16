---
layout: single
title:  "프로그래머스 - 영어 끝말잇기 (Lv.2)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [영어 끝말잇기](https://school.programmers.co.kr/learn/courses/30/lessons/12981)

## 문제 접근
- 끝말잇기가 진행되고 있는 단어를 history에 담는다.
- 마지막 문자로 단어가 시작하는지 확인한다.
- history에 존재하는 단어인지 확인한다.
- **number=i%n+1, turn=i/n+1** 을 계산한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

vector<int> solution(int n, vector<string> words) {
    vector<int> answer;
    
    int number = 0, turn = 0;
    vector<string> history;
    for(int i=0; i<words.size(); i++){
        // 마지막 문자로 시작하는 단어를 말하지 않은 경우
        if(history.size()>0){
            string last = history.back();
            char last_ch = last.back();
            if(last_ch != words[i][0]){
                number = i%n + 1;
                turn = i/n+1;
                break;
            }
        }
        
        if(find(history.begin(), history.end(), words[i])==history.end()){
            history.push_back(words[i]);
        }
        else { // 이전에 등장한 단어를 사용한 경우
            number = i%n + 1;
            turn = i/n + 1;
            break;
        }
    }
    
    answer.push_back(number);
    answer.push_back(turn);
    return answer;
}

```
- **<algorithm>에 있는 find 함수는 찾을 수 없는 경우 end() 반복자를 반환한다.**
- vector, string 컨테이너 모두 **back()** 함수를 지원하여 쉽게 마지막 원소를 확인할 수 있다.