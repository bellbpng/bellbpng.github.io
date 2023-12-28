---
layout: single
title:  "SW Expert Academy - 프로세서 연결하기"
excerpt: "[SW Test 샘플문제] D2"

categories:
  - Samsung SW
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제링크
- [프로세서 연결하기](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV4suNtaXFEDFAUf&categoryId=AV4suNtaXFEDFAUf&categoryType=CODE&problemTitle=&orderBy=INQUERY_COUNT&selectCodeLang=ALL&select-1=&pageSize=10&pageIndex=1)

## 문제설명
- N x N 크기의 cell 이 주어진다. (0은 빈칸, 1은 코어)
- cell 의 가장자리는 전원이 흐르고 있는데 코어를 전선을 이용해서 전원에 연결해야 한다.
    - 전원이 위치한 가장자리에 코어가 있는 경우 전원이 연결된 것으로 간주한다.
    - 전선은 구부릴 수 없다. 즉, 상하좌우 중 하나의 방향으로만 놓여야 하고 다른 전선이나 코어와 겹칠 수 없다.
- 가장 많은 코어를 연결했을 때 전선의 최소 길이를 구하라.   

## 문제접근
- 모든 코어에 방문해서 상하좌우 4가지 방향에 대해서 전원을 연결할 수 있는지 완전탐색(dfs) 을 진행한다.
- 가장자리에 위치한 코어는 이미 전원에 연결되어 있으므로 방문해야 하는 `coreList` 에 담지 않는다.
- 나머지 코어는 `coreList` 에 좌표 정보를 담는다.
- 완전탐색(DFS) 로직은 다음과 같다.
1. core 를 선택한다.
2. 전원을 연결할 방향을 결정한다.
    - 선택한 방향으로 탐색하다가 범위를 벗어나기 전에 1을 만난다면 전선을 놓을 수 없으므로 탐색을 중단한다.
    - 범위를 벗어나게 되면 전원 연결이 가능하므로 해당 경로에 전선을 두고 cell 을 업데이트하고, 전선의 길이를 카운트한다.
3. 재귀호출을 한다.
    - 전원이 연결된 경우 -> **dfs(v+1, coreCount+1, wireLen + count)**
    - 전원이 연결되지 않은 경우 -> **dfs(v+1, coreCount, wireLen)**
4. 마지막 코어에 대한 결정이 끝나면 coreCount 를 비교하며 wireLen 을 갱신한다.

### 구현
```c++
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
#include <memory.h>
#include <climits>
using namespace std;

int N = 0, maxCore = 0, minWire = 0;
int dx[4] = { -1, 1, 0, 0 };
int dy[4] = { 0, 0, -1, 1 };
int board[100][100] = { 0, };
vector<pair<int, int>> coreList;

void connectCoreToPower(int v, int coreCount, int wireLen) {
	if (v == coreList.size()) {
		if (coreCount > maxCore) {
			maxCore = coreCount;
			minWire = wireLen;
		}
		else if (coreCount == maxCore) {
			minWire = min(minWire, wireLen);
		}
		return;
	}

	for (int dir = 0; dir < 4; dir++) {
		int nx = coreList[v].first;
		int ny = coreList[v].second;
		int count = 0;
		while (true) {
			nx += dx[dir];
			ny += dy[dir];
			if (nx < 0 || nx >= N || ny < 0 || ny >= N) break;
			if (board[nx][ny] == 1) {
				count = 0;
				break;
			}
			count++;
		}

		// update board
		int xpos = coreList[v].first;
		int ypos = coreList[v].second;
		for (int i = 0; i < count; i++) {
			xpos += dx[dir];
			ypos += dy[dir];
			board[xpos][ypos] = 1;
		}

		if (count > 0) {
			connectCoreToPower(v + 1, coreCount + 1, wireLen + count);
			// reset board
			xpos = coreList[v].first;
			ypos = coreList[v].second;
			for (int i = 0; i < count; i++) {
				xpos += dx[dir];
				ypos += dy[dir];
				board[xpos][ypos] = 0;
			}
		}
		else if (count==0) {
			connectCoreToPower(v + 1, coreCount, wireLen);
		}
	}
}

int main(int argc, char** argv)
{
	cin.tie(NULL);
	ios_base::sync_with_stdio(false);
	int test_case;
	int T;
	// freopen("input.txt", "r", stdin);
	cin >> T;
	for (test_case = 1; test_case <= T; ++test_case)
	{
		maxCore = INT_MIN;
		minWire = INT_MAX;
		coreList.clear();
		int val = 0;
		cin >> N;
		for (int r = 0; r < N; r++) {
			for (int c = 0; c < N; c++) {
				cin >> val;
				board[r][c] = val;
				if (val == 1) {
					if (r == 0 || r == N - 1 || c == 0 || c == N - 1) continue;
					else coreList.push_back(make_pair(r, c));
				}
			}
		}
		connectCoreToPower(0, 0, 0);
		printf("#%d %d\n", test_case, minWire);
	}
	return 0;//정상종료시 반드시 0을 리턴해야합니다.
}
```
