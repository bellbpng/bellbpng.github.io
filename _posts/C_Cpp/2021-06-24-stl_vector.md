---
layout: single
title:  "C++ STL - vector"
excerpt: "C++ STL - 벡터 컨테이너 멤버함수 정리 "

categories:
  - C/C++
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# vector container?
- 자동으로 힙(heap) 영역에 메모리를 동적할당해주는 일종의 배열
- 데이터 삭제와 삽입이 용이함
- 메모리를 효율적으로 관리하고 예외 처리가 쉬워서 많이 사용됨


## vector 초기화
```c
#include <vector> // 헤더파일 선언

vector<type> name; // 비어있는 벡터 생성
vector<type> name(num); // num만큼 벡터 생성후 0으로 초기화
vector<type> name(num, val); // num만큼 벡터 생성후 val값으로 초기화
```

## vector 멤버함수

### 원소 삽입, 삭제
- v.push_back(element): 마지막 원소 뒤에 element를 삽입
- v.pop_back(): 마지막 원소를 제거

### 접근
- v.begin(): 벡터 컨테이너의 시작 주소를 반환(첫번째 원소를 가리킴), iterator로 활용
- v.end(): 벡터 컨테이너의 마지막 원소 "다음" 주소를 반환(마지막 원소 다음을 가리킴)
- v[idx]: 배열처럼 인덱스를 통한 접근 가능(원소가 담겨있을때만)

### 그 외 자주 사용하는 함수
- v.size(): 원소의 개수를 반환
- v.capacity(): 할당된 공간의 크기를 반환
- v.reverse(n): n개의 원소를 저장할 수 있도록 capacity를 미리 할당, 이때는 메모리만 할당된 상태이므로 push_back() 함수를 호출하여 원소를 삽입하여야한다.
- v.empty(): 벡터가 비어있으면 true, 그렇지 않으면 false 반환
- v.clear(): 원소의 개수를 0으로 만듦(v.size()=0)
- v.erase(iter): iter에 해당하는 원소를 삭제



