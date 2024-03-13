---
layout: single
title:  "프로그래머스 - 파괴되지 않은 건물 (Lv.3) (누적합을 이용한 시간복잡도 감소, 풀이 숙지)"
excerpt: "2022 KAKAO BLIND RECRUITMENT"

categories:
  - Programmers
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제 링크
- 👉 [파괴되지 않은 건물](https://school.programmers.co.kr/learn/courses/30/lessons/92344)

## 문제 설명
- 링크 참고

## 문제 접근
- 게임 맵에 매번 접근하여 내구성을 증감하는 방법으로 문제를 풀 수는 있겠지만, 입력 조건을 봤을 때 O(n*n) 의 시간복잡도는 효율성 테스트에서 무조건 Fail 이다.
- DP, 정렬, .. .등등 아무리 생각해봐도 풀이법이 떠오르지 않아서 아래 접근법을 참고했다.


**참고**

- [카카오테크 문제풀이](https://tech.kakao.com/2022/01/14/2022-kakao-recruitment-round-1/)
- [프로그래머스 질문 게시판](https://school.programmers.co.kr/questions/25471)

- 문제 접근의 핵심은 **누적합** 이다. 누적합을 통해 최종 맵에 더해질 mask 를 만드는 방법이다.
- 예를 들어, 5x5 크기인 맵에 내가 (0,0) ~ (2,2) 까지 k 만큼 증가하는 마스크를 만들고 싶다면 다음과 같은 마스크가 필요하다.
```
k k k 0 0
k k k 0 0
k k k 0 0
0 0 0 0 0
0 0 0 0 0
```
- 이 과정을 누적합을 통해 계산한다. 

### 구현
```c++
#include <string>
#include <vector>
#include <iostream>
#include <memory.h>
#define MAX 1001
using namespace std;

int mask[MAX][MAX];
int N, M;

void updateMask(vector<vector<int>>& skill)
{
    for(int i=0; i<skill.size(); i++)
    {
        int type = skill[i][0];
        int r1 = skill[i][1];
        int c1 = skill[i][2];
        int r2 = skill[i][3];
        int c2 = skill[i][4];
        int degree = skill[i][5];
        if(type==1) degree *= (-1);
        mask[r1][c1] += degree;
        mask[r1][c2+1] += -degree;
        mask[r2+1][c1] += -degree;
        mask[r2+1][c2+1] += degree;
    }
}

void makePrefixSum()
{
    // 모든 행의 누적합
    for(int r=0; r<N; r++)
    {
        for(int c=1; c<M; c++)
        {
            mask[r][c] += mask[r][c-1];
        }
    }
    
    // 모든 열의 누적합
    for(int c=0; c<M; c++)
    {
        for(int r=1; r<N; r++)
        {
            mask[r][c] += mask[r-1][c];
        }
    }
}

int addMask(vector<vector<int>>& board)
{
    int ret = 0;
    for(int r=0; r<N; r++)
    {
        for(int c=0; c<M; c++)
        {
            board[r][c] += mask[r][c];
            if(board[r][c] > 0) ret++;
        }
    }
    return ret;
}

int solution(vector<vector<int>> board, vector<vector<int>> skill) {
    int answer = 0;
    memset(mask, 0, sizeof(mask));
    N = board.size();
    M = board[0].size();
    updateMask(skill);
    makePrefixSum();
    answer = addMask(board);
    
    return answer;
}
```
