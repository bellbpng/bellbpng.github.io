---
layout: single
title:  "C++ STL - cctype"
excerpt: "C++ STL - cctype "

categories:
  - C/C++
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 헤더파일 cctype (ctype.h)

### functions
- isalnum: 알파벳 or 숫자면 true
- isalpha: 알파벳이면 true
- isblank: space 이거나 tab 이면 true
- isdigit: 0~9이면 true
- isxdigit: 16진수이면 true
- islower: 소문자이면 true
- isupper: 대문자이면 true
- isspace: white space면 true
- tolower(): 대문자->소문자로 변환하여 반환
- toupper(): 소문자->대문자로 변환하여 반환

### 사용예시

```c
#include <iostream>
#include <string>
#include <cctype>
using namespace std;

// is+something 함수의 경우 if문과 결합

int main()
{
    string str1 = "My name is Bokyoung";
    string str2 = "Hello world!";

    for (int i = 0; i < str1.length(); i++) {
        if (islower(str1[i])) {
            str1[i] = toupper(str1[i]);
        }
    }
    cout << str1 << endl;

    for (int i = 0; i < str2.length(); i++) {
        if (isupper(str2[i])) {
            str2[i] = tolower(str2[i]);
        }
    }
    cout << str2 << endl;
}

```