---
layout: single
title:  "보글게임"
excerpt: "완전탐색, 재귀"

# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제출처
- [알고리즘 문제해결 전략1] - 6장 완전탐색 예제
- 소스코드는 구현한 함수의 동작 여부를 관찰하기 위해 테스트 코드를 추가하여 작성한 결과이다.

## 문제 설명
- 5x5 크기의 배열에 알파벳이 적혀있다.
- 상하좌우/대각선으로 인접한 칸들의 글자들을 이어서 단어를 찾아야 한다.
- 대각선만으로도 쭉 이어질 수 있고, 한 글자가 두번 이상 사용될 수 있다.
- hasWord(y,x,word) 함수를 작성하여 보글 게임판의 (y,x)에서 시작하는 단어 word의 존재 여부를 반환한다.

## 문제 접근 방향 (Solution)
- 8가지의 방향을 정한다. (상하좌우/대각선)
- 재귀함수가 호출될 때마다 찾는 단어의 앞글자를 비교하여 이동한 좌표의 문자와 일치하는지 확인한다.
  - 재귀함수 호출 시 전달되는 문자열은 앞글자가 하나씩 줄어든 형태여야 한다.
- 문자와 일치하면 true를 반환하고 탐색을 계속 진행하고, 일치하지 않으면 false를 반환하고 탐색을 중단한다.
- 기저 사례는 다음과 같다. (재귀함수 탈출 조건)
  1. 이동한 좌표가 배열 범위를 벗어나면 false 반환
  2. 단어의 앞글자와 현재 좌표의 문자가 일치하지 않으면 false 반환
  3. 위 두가지 경우가 아니면 true 반환
- 재귀는 이동한 좌표에 대해서 8가지 방향에 대해 완전탐색을 진행한다.

** 위 문제는 게임판에서의 현재 위치(y,x) 그리고 단어 word가 주어질 때 해당 단어를 이 칸에서부터 시작해서 찾을 수 있는가? 로 정의된다.**

- 현재 위치(y,x)에 단어의 첫 글자가 있는가?
- 윗 칸(y-1,x)에서 시작해서, 단어의 나머지 글자들을 찾을 수 있는가?
- 왼쪽 대각선 칸(y-1,x-1)에서 시작해서 단어의 나머지 글자들을 찾을 수 있는가?
- ...
- ...

** 원래 문제를 형식이 같은 여러 개의 조각으로 나누어 문제를 푼 결과이다. **


## 구현 결과

```c++

#include <iostream>
#include <string>
using namespace std;

int dx[8] = { -1,-1,0,1,1,1,0,-1 };
int dy[8] = { 0,1,1,1,0,-1,-1,-1 };
char alpha_map[5][5];

bool inRange(int y, int x)
{
	if ((y >= 0 && y < 5) && (x >= 0 && x < 5))
		return true;
	else
		return false;
}

bool hasWord(int y, int x, string word)
{
	// 기저 사례1: 좌표가 범위 밖인 경우
	if (!inRange(y, x)) return false;
	// 기저 사례2: 첫 글자가 일치하지 않는 경우
	if (word[0] != alpha_map[y][x]) return false;
	// 기저 사례3: 단어 길이가 1인 경우 성공
	if (word.size() == 1) return true;

	for (int dr = 0; dr < 8; dr++)
	{
		int next_x = x + dx[dr];
		int next_y = y + dy[dr];
		
		if (hasWord(next_y, next_x, word.substr(1)))
			return true;
	}
	return false;
}

int main()
{
	cin.tie(NULL);
	ios_base::sync_with_stdio(false);

	for (int r = 0; r < 5; r++)
		for (int c = 0; c < 5; c++)
			cin >> alpha_map[r][c];

	getchar();
	string word;
	int x, y;
	cin >> word;
	cin >> y >> x;

	if (hasWord(y, x, word))
		cout << "exist" << endl;
	else
		cout << "no exist" << endl;

	return 0;
}

```

### 간결한 코드 작성 팁
- 입력이 잘못되거나 범위에서 벗어난 경우도 기저 사례로 택하여 맨 처음에 처리하도록 한다.