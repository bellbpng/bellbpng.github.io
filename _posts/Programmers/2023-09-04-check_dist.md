---
layout: single
title:  "프로그래머스 - 거리두기 확인하기 (Lv.2)"
excerpt: "2021 카카오 채용연계형 인턴십"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- 👉 [거리두기 확인하기](https://school.programmers.co.kr/learn/courses/30/lessons/81302)

## 문제 설명
- 5개의 대기실이 주어지며 각 대기실은 5x5 크기이다.
- 거리두기를 위하여 맨해튼 거리가 2이하로 앉을 수 없게 하려고 한다.
    - 맨해튼 거리는 `abs(x1-x2) + abs(y1-y2)` 를 의미한다.
- 단, 응시자가 앉은 자리 사이가 파티션으로 막혀 있을 경우에는 거리가 2여도 가능하다.
- `P` 응시자가 앉은 자리, `O` 는 빈 테이블, `X` 는 파티션을 의미한다.

```
PXP ---> OK!
------
PX
XP ---> OK!
------
PX
OP ---> No!
------
```

## 문제 접근
- 응시자가 앉은 자리(P)에서 모든 P까지의 거리를 구한다.
    - 응시자와 거리가 1인 경우는 거리두기가 지켜지지 않는 경우이므로 해당 대기실은 0 을 반환한다.
    - 응시자와 거리가 2인 경우는 테이블 위치를 확인해야하므로 `cand` 배열에 담아둔다.
- `cand` 배열에 담긴 좌표와 시작점(sx, sy) 사이에 테이블이 존재하는지 확인한다.
    - 같은 행 혹은 같은 열에 위치하는 경우 중간 좌표를 확인하면 된다.
    - 위의 경우가 아닌 경우는 서로 대각선에 위치하는 경우이므로 2x2 격자에서 두 점을 제외한 나머지 점에 테이블이 존재하는지 확인한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <memory.h>
#include <queue>
using namespace std;

vector<string> cboard;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

bool search(int sx, int sy){
    vector<pair<int, int>> cand;
    int dist[5][5];
    bool visited[5][5];
    memset(dist, 0, sizeof(dist));
    memset(visited, false, sizeof(dist));
    
    queue<pair<int, int>> q;
    q.push(make_pair(sx, sy));
    dist[sx][sy] = 0;
    visited[sx][sy] = true;
    
    while(!q.empty()){
        pair<int, int> cpos = q.front();
        q.pop();
        
        for(int dir=0; dir<4; dir++){
            int nx = cpos.first + dx[dir];
            int ny = cpos.second + dy[dir];
            if(nx<0 || nx>=5 || ny<0 || ny>=5) continue;
            if(visited[nx][ny]) continue;
            visited[nx][ny] = true;
            q.push(make_pair(nx, ny));
            dist[nx][ny] = dist[cpos.first][cpos.second]+1;
            if(cboard[nx][ny]=='P'){
                // 거리가 1인 경우는 바로 옆이므로 거리두기가 항상 지켜지지 않음
                if(dist[nx][ny]==1) return false;
                if(dist[nx][ny]==2) cand.push_back(make_pair(nx, ny));
            }
        }
    }

    // 두 점 사이에 테이블이 놓여 있는지 확인
    for(auto ele:cand){
        // 같은 행에 있는 경우
        if(sx==ele.first){
            int my = (sy + ele.second)/2;
            if(cboard[sx][my]!='X') return false; 
        }
        // 같은 열에 있는 경우
        else if(sy==ele.second){
            int mx = (sx + ele.first)/2;
            if(cboard[mx][sy]!='X') return false;
        }
        // 대각선에 있는 경우
        else {
            int x1 = sx;
            int y1 = ele.second;
            int x2 = ele.first;
            int y2 = sy;
            if(cboard[x1][y1]!='X' || cboard[x2][y2]!='X') return false;
        }
    }
    
    return true;
}

int checkSafeZone(){
    // 응시자가 앉은 자리를 찾은 후 다른 응시자와의 거리를 구함
    for(int r=0; r<5; r++){
        for(int c=0; c<5; c++){
            if(cboard[r][c]=='P'){
                if(search(r,c)==false) return 0; 
            }
        }
    }
    return 1;
}

vector<int> solution(vector<vector<string>> places) {
    vector<int> answer;
    
    for(int i=0; i<places.size(); i++){
        cboard.clear();
        for(int j=0; j<5; j++){
            cboard.push_back(places[i][j]);
        }
        answer.push_back(checkSafeZone());
    }
    
    
    return answer;
}
```
