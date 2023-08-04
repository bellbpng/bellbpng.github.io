---
layout: single
title:  "[SOFTEER] 스마트물류 "
excerpt: "그리디(Greedy)"

categories:
  - Softeer
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 문제출처
- [스마트물류](https://softeer.ai/practice/info.do?idx=1&eid=414&sw_prbl_sbms_sn=236738)

## 문제접근
- DFS를 이용해서 완전탐색을 시도했다가 시간초과가 났다.
- 그리디 알고리즘으로 해결할 수 있다. 즉, 매순간 이익이 최대가 되는 선택을 하면 된다.
    - 로봇은 본인과 가까운 부품을 집어야 전체 시스템에서 최대 성능을 낼 수 있다. (가장 많이 물건을 잡을 수 있음)

### 구현
```c++
#include <iostream>
#include <string>
#include <vector>
#include <cmath>
using namespace std;

int main(int argc, char** argv)
{
	int n, k;
	cin >> n >> k;
	string line;
	cin >> line;
	vector<int> robot, package;
	for(int i=0; i<line.size(); i++){
		if(line[i]=='P') robot.push_back(i);
		if(line[i]=='H') package.push_back(i);
	}

	int ans=0, idxR=0, idxM=0;
	while(idxR < robot.size() && idxM < package.size()){
		if(abs(robot[idxR]-package[idxM]) <= k){
			idxR++; idxM++;
			ans++;
		}
		else if(robot[idxR] > package[idxM]) idxM++;
		else if(package[idxM] > robot[idxR]) idxR++;
	}

	cout << ans << endl;
	return 0;
}
```
