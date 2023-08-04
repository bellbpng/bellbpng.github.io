---
layout: single
title:  "[SOFTEER] 1회 정기 코딩 인증평가 기출 - 로봇이 지나간 경로 "
excerpt: "DFS, 백트래킹"

categories:
  - Softeer
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제출처
- 소프티어 HSAT 3회 정기 코딩 인증평가 기출
- [로봇이 지나간 경로](https://softeer.ai/practice/info.do?idx=1&eid=577)

## 문제접근
- 로봇이 움직일 수 있는 방법을 먼저 찾아본다. "A" 라는 명령어가 있어야만 로봇은 상하좌우로 2칸씩 움직일 수 있으므로 모든 명령어에는 방향 전환 여부와 함께 "A"가 들어가야 한다. 이때, 명령어의 길이가 최소가 되는 각각의 경우는 다음과 같다.
    - "A": 현재 방향에서 두칸 이동
    - "LA": 현재 방향에서 왼쪽으로 90도 회전 후 두칸 이동
    - "LLA": 현재 방향에서 왼쪽으로 180도 회전 후 두칸 이동
    - "RA": 현재 방향에서 오른쪽으로 90도 회전 후 두칸 이동
- 로봇의 경로가 먼저 주어지므로, 반드시 방문해야하는 좌표들을 벡터 컨테이너 **cand** 에 담아둔다. 이 배열의 좌표들로 Dfs, 백트래킹을 적용하면 된다.
- 기저사례는 빙문해야 할 모든 좌표를 방문한 경우이다. 이때 완성된 명령어의 길이가 기존에 완성된 명령어의 길이보다 짧다면 정답으로 출력할 명령어와 출발좌표, 방향을 갱신한다.

### 구현
```c++
#include <iostream>
#include <vector>
#include <queue>
#include <memory.h>
using namespace std;

char board[27][27];
bool visited[27][27] = {false, };
vector<pair<int, int>> cand; // 방문해야하는 좌표
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, -1, 0, 1};
int sx, sy, sd, ansx, ansy, ansd;
string ansCmd;
int H, W;

void Dfs(int x, int y, int dir, int cnt, string cmd){
	// 기저사례: 방문해야할 모든 좌표 방문
	if(cand.size()==cnt){
		if(ansCmd.size()==0){ // 한번도 완성된 적이 없는 경우
			ansCmd = cmd;
			ansx = sx, ansy = sy, ansd = sd;
			return;
		}
		else if(ansCmd.size() > cmd.size()){
			ansCmd = cmd;
			ansx = sx, ansy = sy, ansd = sd;
			return;
		}
	}

	// i=0: A, i=1: LA, i=2: LLA, i=3: RA
	for(int i=0; i<4; i++){
		string nmov = "A";
		int nx1, ny1, nx2, ny2, nd;
		if(i==1) nmov = "LA";
		else if(i==2) nmov = "LLA";
		else if(i==3) nmov = "RA";
		nd = (dir+i)%4;
		nx1 = x + dx[nd]; ny1 = y + dy[nd];
		nx2 = x + 2*dx[nd]; ny2 = y + 2*dy[nd];
		if(nx2<1 || nx2>H || ny2<1 || ny2>W) continue;
		if(board[nx1][ny1]!='#'||board[nx2][ny2]!='#'||visited[nx1][ny1]||visited[nx2][ny2]) continue;
		visited[nx1][ny1]=true; visited[nx2][ny2]=true;
		Dfs(nx2, ny2, nd, cnt+2, cmd+nmov);
		visited[nx1][ny1]=false; visited[nx2][ny2]=false;
	}
}

int main(int argc, char** argv)
{
	scanf("%d %d", &H, &W);
	getchar();
	for(int r=1; r<=H; r++){
		for(int c=1; c<=W; c++){
			scanf("%c", &board[r][c]);
			if(board[r][c]=='#') 
				cand.push_back(make_pair(r,c));
		}
		getchar();
	}
	for(auto ele:cand){
		sx = ele.first; sy = ele.second;
		for(int dir=0; dir<4; dir++){
			memset(visited, false, sizeof(visited));
			sd = dir;
			visited[sx][sy]=true;
			Dfs(sx, sy, sd, 1, "");
			visited[sx][sy]=false;
		}
	}
	char ch;
	if(ansd==0) ch = '^';
	else if(ansd==1) ch = '<';
	else if(ansd==2) ch = 'v';
	else if(ansd==3) ch = '>';
	printf("%d %d\n", ansx, ansy);
	printf("%c\n", ch);
	cout << ansCmd << endl;
	return 0;
}
```

