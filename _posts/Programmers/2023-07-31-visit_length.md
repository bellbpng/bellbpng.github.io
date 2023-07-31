---
layout: single
title:  "프로그래머스 - 방문 길이 (Lv.2)"
excerpt: "Summer/Winter Coding(~2018)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [방문 길이](https://school.programmers.co.kr/learn/courses/30/lessons/49994)

## 문제 접근
- 이동한 좌표의 history를 담을 수 있도록 배열을 만든다.
    - **(history[0][0], history[0][1]) -> (history[0][2], history[0][3])** 의 좌표 이동을 의미
- 좌표를 움직일 때마다 history에서 해당 이력이 있는지 확인한다.
    - 이때, A->B 로 이동하는 경우와 B->A 로 이동하는 경우는 같은 이력으로 포함해야 한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <queue>
using namespace std;

bool CheckHistory(vector<vector<int>>& history, int hidx){
    pair<int, int> cfrom = {history[hidx][0], history[hidx][1]};
    pair<int, int> cto = {history[hidx][2], history[hidx][3]};
    pair<int, int> bfrom, bto;
    for(int i=0; i<hidx; i++){
        bfrom = {history[i][0], history[i][1]};
        bto = {history[i][2], history[i][3]};
        if(cfrom==bfrom && cto==bto) return false;
        else if(cfrom==bto && cto==bfrom) return false;
    }
    return true;
}

bool CheckArea(int x, int y){
    if(x>=-5 && x<=5 && y>=-5 && y<=5)
        return true;
    else
        return false;
}

int solution(string dirs) {
    int answer = 1; // 원점에서 출발할 때는 항상 처음 걸어본 길
    vector<vector<int>> history(500, vector<int>(4, 0));
    
    int cx=0, cy=0, nx, ny, hidx=0;
    for(int i=0; i<dirs.size(); i++){
        if(dirs[i]=='U'){
            nx = cx, ny = cy+1;
        }
        else if(dirs[i]=='D'){
            nx = cx, ny = cy-1;
        }
        else if(dirs[i]=='R'){
            nx = cx+1, ny = cy;
        }
        else if(dirs[i]=='L'){
            nx = cx-1, ny = cy;
        }
        if(CheckArea(nx, ny)==false) continue;
        history[hidx++] = {cx, cy, nx, ny};  
        cx=nx, cy=ny;
        if(hidx==1) continue;
        if(CheckHistory(history, hidx-1)) answer++;
    }
    
    return answer;
}

```

