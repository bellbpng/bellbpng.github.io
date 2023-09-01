---
layout: single
title:  "프로그래머스 - 행렬 테두리 회전하기 (Lv.2)"
excerpt: "2021 Dev-Matching: 웹 백엔드 개발자(상반기)"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- 👉 [행렬 테두리 회전하기](https://school.programmers.co.kr/learn/courses/30/lessons/77485)

## 문제 설명
- rows x columns 크기인 행렬이 주어진다. 행렬의 원소는 1행부터 열을 따라 1부터 채워진다.
- 행렬은 테두리만 시계방향으로 회전을 하고, 각 회전은 (x1, y1, x2, y2)의 좌표 정보를 가지고 x1행 y1열부터 x2행 y2열까지의 영역의 테두리에서 이루어진다.
- 각 회전에서 위치가 바뀌는 원소의 최솟값을 배열에 담아서 반환하라.

## 문제 접근
- 테두리 회전이므로 가로선 2개, 세로선 2개 영역만 회전을 시켜주면 된다.
- 겹치는 영역에 주의한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <memory.h>
#include <iostream>
using namespace std;
int n, m;
int board[101][101];

int rotateBoard(int x1, int y1, int x2, int y2){
    int nboard[101][101];
    memcpy(nboard, board, sizeof(board));
    int minVal = 1e9;
    // 가로선
    for(int c=y1; c<=y2; c++){
        minVal = min(minVal, board[x1][c]);
        minVal = min(minVal, board[x2][c]);
        if(c==y1) {
            nboard[x1][c] = board[x1+1][c];
            nboard[x2][c] = board[x2][c+1];
        }
        else if(c==y2){
            nboard[x1][c] = board[x1][c-1];
            nboard[x2][c] = board[x2-1][c];
        }
        else{
            nboard[x1][c] = board[x1][c-1];
            nboard[x2][c] = board[x2][c+1];
        }
    }
    
    // 세로선 (중간 부분만)
    for(int r=x1+1; r<=x2-1; r++){
        minVal = min(minVal, board[r][y1]);
        minVal = min(minVal, board[r][y2]);
        nboard[r][y1] = board[r+1][y1];
        nboard[r][y2] = board[r-1][y2];
    }
    memcpy(board, nboard, sizeof(nboard));
    // cout << minVal << " ";
    return minVal;
}

vector<int> solution(int rows, int columns, vector<vector<int>> queries) {
    vector<int> answer;
    n = rows; m = columns;
    
    // init board
    int val = 1;
    for(int r=1; r<=n; r++){
        for(int c=1; c<=m; c++)
            board[r][c] = val++;
    }
    
    for(int i=0; i<queries.size(); i++){
        answer.push_back(rotateBoard(queries[i][0],queries[i][1],queries[i][2],queries[i][3]));
    }
    
    return answer;
}
```
