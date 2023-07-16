---
layout: single
title:  "프로그래머스 - 숫자 문자열과 영단어 (Lv.2)"
excerpt: "2021 카카오 채용연계형 인턴십"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [숫자 문자열과 영단어](https://school.programmers.co.kr/learn/courses/30/lessons/81301)

## 문제 접근
- map 자료구조를 활용하여 영단어와 숫자의 관계를 나타내는 table을 만든다.
- 문자열 탐색 중 알파벳 문자인 경우 문자열 임시 변수(tmp)에 담으며 table에 존재하는지 확인한다.
    - table에 존재하면 해당 문자열을 숫자로 변환하여 저장한다.
- 숫자인 경우 그대로 저장한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <cctype>
#include <map>

using namespace std;

int solution(string s) {
    int answer = 0;
    
    // init
    string result;
    map<string, char> table;
    table["zero"] = '0'; table["one"] = '1';
    table["two"] = '2'; table["three"] = '3';
    table["four"] = '4'; table["five"] = '5';
    table["six"] = '6'; table["seven"] = '7';
    table["eight"] = '8'; table["nine"] = '9';
    
    string tmp;
    for(int i=0; i<s.length(); i++){
        if(isdigit(s[i])){
            result.push_back(s[i]);
        }
        else{ // 알파벳인 경우
            tmp.push_back(s[i]);
            if(table.find(tmp)==table.end()) continue;
            result.push_back(table[tmp]);
            tmp.clear();
        }
    }
    
    answer = stoi(result);
    
    return answer;
}

```