---
layout: single
title:  "프로그래머스 - [3차] 파일명 정렬 (Lv.2)"
excerpt: "2018 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [파일명 정렬](https://school.programmers.co.kr/learn/courses/30/lessons/17686)

## 문제 접근
- 구조체를 만들어서 주어진 파일명을 **파일명, HEAD, NUMBER, 들어온 순서** 대로 table을 만든다.
    - tail에 대한 정보는 필요 없다.
    - HEAD 는 앞에서부터 숫자를 만날때까지 탐색하면 된다.
    - NUMBER 는 HEAD 이후의 문자부터 탐색하여 숫자가 아닌 문자를 만날때까지 탐색하면 된다.
- sort() 함수를 호출하여 조건에 맞도록 정렬한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <cctype>
#include <algorithm>

using namespace std;

typedef struct _fileInfo{
    string filename;
    string head;
    int number;
    int index;
} FileInfo;

bool cmp(FileInfo f1, FileInfo f2){
    if(f1.head != f2.head){
        return f1.head < f2.head;
    }
    if(f1.number != f2.number){
        return f1.number < f2.number;
    }
    return f1.index < f2.index;
}

vector<string> solution(vector<string> files) {
    vector<string> answer;
    
    // parsing
    vector<FileInfo> table;
    string tname, thead, tnumber;
    int tindex;
    for(int index=0; index<files.size(); index++){
        tname.clear(), thead.clear(), tnumber.clear();
        tname = files[index]; tindex = index;
        string file = files[index];
        int idx = 0;
        // head 결정 (소문자)
        for(int i=0; i<file.size(); i++){
            if(isdigit(file[i])) break;
            if(isupper(file[i])) {
                file[i] = tolower(file[i]);
            }
            thead.push_back(file[i]);
            idx = i;
        }
        // number 결정
        for(int i=idx+1; i<file.size(); i++){
            if(!isdigit(file[i])) break;
            tnumber.push_back(file[i]);
        }
        
        FileInfo fi = {tname, thead, stoi(tnumber), tindex};
        table.push_back(fi);
    }
    
    sort(table.begin(), table.end(), cmp);
    for(int i=0; i<table.size(); i++){
        answer.push_back(table[i].filename);
    }
    
    return answer;
}
```
