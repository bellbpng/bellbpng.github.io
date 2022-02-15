---
layout: single
title:  "C++ STL - string"
excerpt: "C++ STL - string 클래스 멤버함수 정리 "

categories:
  - C/C++
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# string class? 
- 문자열을 다루는 클래스
- 다양한 멤버함수를 통해 문자열을 보다 쉽게 처리할 수 있다.


## string 초기화
```c
#include <string> // 헤더파일 선언

string str1("string class");

string str2;
str2 = "string class";

string str3(str2);
```

## string 멤버함수

### 접근
- str[idx]로 인덱스를 통해 문자 하나하나에 접근 가능하다.
- str.front(): 맨 앞에 문자를 반환
- str.back(): 맨 뒤 문자를 반환

### 문자 삽입, 삭제
- str.push_back(ch): 문자열 맨 뒤에 문자 삽입
- str.pop_back(): 문자열 맨 뒤 문자 삭제
- str.append(str2): 문자열 뒤에 문자열 붙이기
- str.insert(index, ch): str[index]에 문자 삽입

### 숫자 -> 문자열, 문자열 -> 숫자 변환
- to_string(element): element를 문자열로 변환
- stoi(const string&str, size_t* idx = 0, int base = 10): string to int
- stof(const string&str, size_t* idx = 0, int base = 10): string to float
- stol(const string&str, size_t* idx = 0, int base = 10): string to long
- stod(const string&str, size_t* idx = 0, int base = 10): string to double

```c
const string&str : 숫자로 변환할 문자열을 지정
size_t *idx : 변환과정에서 문자를 읽으면 해당 위치를 저장.
-----------------
예를들어, 
string str = "97Bokyoung";
size_t sz = stoi(str, &sz, 10);
str[2]=="B" 이므로 sz = 2 가 대입된다.
세번째 인자까지 사용하는데 두번째인자를 사용하지 않을거라면 nullptr을 전달해주면 된다.
-----------------
int base : 변환할 진수를 결정하는 인자.

------ 정수 문자열의 형태가 포함할 수 있는 요소들 ------
1. 부호: 문장 맨 앞에 +, -  부호를 포함할 수 있다.
2. 8진법임을 알려주는 접두사 '0'
3. 16진법임을 알려주는 접두사 "0x"
4. 문장 맨 앞에 오는 공백은 공백이 아닌 문자를 찾을때까지 무시한다.
```

### 그 외 자주 사용하는 함수
- str.length(): 문자열의 길이를 반환. size() 함수와 같다.
- str.erase(index, count): str[index]부터 count개 문자를 지운다.
- str.compare(string&str2): 매개변수로 들어온 str2와 비교해서 같으면 0, str이 str2보다 사전순으로 빠르면 -1, 그렇지 않으면 +1 반환
- str.find("sub", start index): 부분문자열을 찾는 함수. 해당 문자열을 발견하면 첫번째 인덱스를 반환한다. 해당 문자열이 없다면 string::npos 를 반환한다.


