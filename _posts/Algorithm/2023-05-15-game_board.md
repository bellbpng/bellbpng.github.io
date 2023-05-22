---
layout: single
title:  "[알고리즘 문제해결 전략] 게임판"
excerpt: "완전탐색, 재귀"

categories:
  - Algorithm
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제출처
- [알고리즘 문제해결 전략1] - 6장 완전탐색 예제
- 소스코드는 구현한 함수의 동작 여부를 관찰하기 위해 본인의 테스트 코드를 추가하여 작성한 결과이다.

## 문제 설명
- H x W 크기의 게임판이 검은 칸, 흰 칸으로 구성되어 격자 모양을 하고 있음
- 세 칸짜리 L자 모양의 블록으로 흰 칸을 덮으려고 함
- 블록을 덮을 때 겹치거나, 검은 칸을 덮거나, 게임판 밖으로 나가면 안됨
- 게임판이 주어질 때 덮는 방법의 수를 구하기
- 흰 칸의 수는 50을 넘지 않음

## 문제 접근 방향 (Solution)
- 흰 칸의 수가 3의 배수가 아닌 경우 덮을 수 없다.
- 흰 칸에 4개의 L모양의 블록 중 하나를 덮고, 남은 흰 칸에 대해 재귀호출을 한다.
- 블록을 놓는 순서는 중요하지 않으니, 일관된 블록을 놓는 방법을 정해야 중복으로 세는 문제를 막을 수 있다. 본 풀이에서는 가장 윗줄에서 왼쪽에 위치한 칸을 시작칸으로 설정한다.
- 가능한 블록 모양을 배열에 담아두고 시작한다. (코드와 데이터를 분리한다.)

## 구현 결과

```c++

#include <iostream>
#include <vector>
#include <string>
using namespace std;

// [type][칸][상대좌표]
// type별로 각 칸의 상대좌표를 넣어둔다. 윗줄 가장 왼쪽에 위치하는 좌표를 (0,0) 기준좌표로 설정
const int coverType[4][3][2] = {
	{ {0, 0}, {1, 0}, {1, -1}, },
	{ {0, 0}, {0, 1}, {1, 1}, },
	{ {0, 0}, {1, 0}, {1, 1}, },
	{ {0, 0}, {1, 0}, {0, 1}, },
};

// board[r][c]에서 type번 방법으로 덮거나, 덮었던 블록을 없앤다.
// delta=1이면 덮고, delta=-1이면 블록을 없앤다.
// 블록을 덮을 수 없는 경우(범위를 벗어나거나, 검은칸을 덮거나, 이전에 놓은 블록과 겹치는 경우) false 반환
bool set(vector<vector<int>>& board, int r, int c, int type, int delta)
{
	bool ok = true;
	for (int i = 0; i < 3; i++) // 덮어야 하는 칸이 3개
	{
		int next_r = r + coverType[type][i][0]; // type번 블록의 i번 칸의 x좌표
		int next_c = c + coverType[type][i][1]; // type번 블록의 i번 칸의 y좌표
		if (next_r < 0 || next_r >= board.size() || next_c < 0 || next_c >= board[0].size()) // 범위를 벗어나는 경우
			ok = false;
        // delta=1(덮는 과정), 덮여 있지 않았던 칸 -> 1, 덮여 있던 칸 -> 2
        // delta=-1(빼는 과정), 덮여 있지 않았던 칸 -> -1, 덮여 있던 칸 -> 0
		else if ((board[next_r][next_c] += delta) > 1) 
			ok = false;
	}
	return ok;
}

// board의 모든 빈 칸을 덮을 수 있는 방법의 수를 반환한다.
// board[i][j]=1 이면 덮인 칸 혹은 검은 칸
// board[i][j]=0 이면 아직 덮이지 않은 칸
int cover(vector<vector<int>>& board)
{
	// 아직 채우지 못한 칸 중 가장 윗줄 왼쪽에 위치한 좌표를 찾는다.
	int r = -1, c = -1;
	for (int i = 0; i < board.size(); i++)
	{
		for (int j = 0; j < board[i].size(); j++)
		{
			if (board[i][j] == 0)
			{
				r = i;
				c = j;
				break;
			}
		}
		if (r != -1) break;
	}
	// 기저 사례: 모든 칸을 채웠으면 1을 반환한다.
	if (r == -1) return 1;
	int ret = 0;
	for (int type = 0; type < 4; type++)
	{
		// 만약 bound[r][c]를 type형태로 덮을 수 있으면 재귀호출한다.
		if (set(board, r, c, type, 1))
			ret += cover(board);
		// 덮었던 블록을 치운다.
		set(board, r, c, type, -1);
	}
	return ret;
}

int main()
{
	int C;
	cin >> C;
	for (int test_case = 0; test_case < C; test_case++)
	{
		int H, W;
		int white_num = 0;
		vector<vector<int>> board;
		string str;
		cin >> H >> W;
		for (int i = 0; i < H; i++)
		{
			cin >> str;
			vector<int> v;
			for (auto ele : str)
			{
				if (ele == '#') // 검은 칸
					v.push_back(1);
				else if (ele == '.') // 흰 칸
				{
					v.push_back(0);
					white_num += 1;// 흰 칸 개수 세기
				}
			}
			board.push_back(v);
		}
		int ans = 0;
		if (white_num % 3) // 흰 칸의 개수가 3의 배수가 아니면 탐색할 필요 x
			cout << ans << endl;
		else
		{
			ans = cover(board);
			cout << ans << endl;
		}
	}
	return 0;
}

```

### Test Case
input
```
3
3 7
#.....#
#.....#
##...##
3 7
#.....#
#.....#
##..###
8 10
##########
#........#
#........#
#........#
#........#
#........#
#........#
##########
```

output
```
0
2
1514
```
