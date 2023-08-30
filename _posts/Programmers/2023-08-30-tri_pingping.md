---
layout: single
title:  "프로그래머스 - 삼각 달팽이 (Lv.2)"
excerpt: "월간 코드 챌린지 시즌1"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- [삼각 달팽이](https://school.programmers.co.kr/learn/courses/30/lessons/68645)

## 문제 설명
- 정수 n이 매개변수로 주어지고, 밑변의 길이와 높이가 n인 피라미드 모양의 삼각형이 주어진다.
- 삼각형의 맨 위 꼭짓점부터 반시계 방향으로 1부터 숫자를 채우게 된다. 첫 행부터 마지막 행까지 모든 원소를 새로운 배열에 받아서 return 하는 함수를 구현하라.

## 문제 접근
- 숫자가 채워지는 방향은 반드시 남쪽 방향으로 시작하여, 동쪽 -> 북서쪽 -> 남쪽 ... 으로 변해가며 피라미드를 채워야 한다.
- 3가지 방향에 대해서 남쪽, 동쪽, 북서쪽 순서대로 방향 데이터를 정해두고, 2차원 배열에서 좌표를 벗어나거나 방문했던 곳을 재방문하게 되면 방향을 바꿔줄 수 있도록 한다.

### 구현(All Pass)
```c++
#include <string>
#include <vector>
#include <memory.h>
#include <iostream>
using namespace std;

vector<int> solution(int n) {
    int board[1000][1000];
    int visited[1000][1000];
    // 남쪽, 동쪽, 북서쪽 방향
    // 반드시 이 순서대로 진행해야함
    int dx[3] = {1, 0, -1}; 
    int dy[3] = {0, 1, -1};
    
    vector<int> answer;
    
    // 전체 칸의 개수
    int len = 0;
    for(int i=1; i<=n; i++){
        len += i;
    }
    
    memset(board, 0, sizeof(board));
    memset(visited, false, sizeof(visited));
    int sx = 0, sy = 0, dir = 0;
    board[sx][sy]=1;
    visited[sx][sy]=true;
    
    for(int i=2; i<=len; i++){
        int nextX = sx + dx[dir];
        int nextY = sy + dy[dir];
        if(nextX<0 || nextX>=n || nextY<0 || nextY>=n){
            dir = (dir+1)%3;
        }
        else if(visited[nextX][nextY]){
            dir = (dir+1)%3;
        }
        nextX = sx + dx[dir];
        nextY = sy + dy[dir];
        board[nextX][nextY] = i;
        visited[nextX][nextY]=true;
        sx = nextX, sy = nextY;
    }
    
    for(int r=0; r<n; r++){
        for(int c=0; c<n; c++){
            if(board[r][c]!=0){
                answer.push_back(board[r][c]);
            }
        }
    }
    
    return answer;
}
```
