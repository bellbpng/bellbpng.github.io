---
layout: single
title:  "CentOS 환경에서 GCC 설치 및 컴파일 "
excerpt: "Linux, CentOS"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# GCC 설치 및 컴파일

## GCC(the GNU Compiler Collection) 란?
- GNU 프로젝트의 오픈 소스 컴파일러 컬렉션, 리눅스 계열 플랫폼의 사실상 표준 컴파일러
- 지원 언어
    - `C/C++`, `Objective-C`, `Fortan`, `Ada`, `Go`

## Yum (Yellow dog Updater, Modified)
- redhat 계열에서 패키지 관리 프로그램인 RPM 기반의 시스템을 위한
자동 업데이터 겸 패키지 설치/제거 도구
- 페도라, CentOS 등 많은 RPM 기반 리눅스 배포판에서 사용
- 우분투 등 데비안 계열의 apt(Advanced Packaging Tool)와 유사
- **기본 사용법**
    - 패키지 설치 : yum install 패키지명
    - 패키지 삭제 : yum remove 패키지명
    - 패키지 업그레이드 : yum update 패키지명
    - 패키지 조회 : yum search 패키지명
    - 패키지 목록 : yum list 패키지명
    - yum 데이터베이스 동기화 업데이트 : yum update
- dnf: CentOS 8 에서 도입된 command, 기존 yum과 동일

## 설치 명령어
- `yum -y install gcc` : C 컴파일러 설치
- `yum -y install gcc-c++` : C++ 컴파일러 설치
- `-y` 옵션은 설치 중간에 나오는 입력란을 자동으로 y 처리 해줌

## GCC 컴파일러 처리 과정
<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/6a787dee-a450-4409-b6c5-11f64c01d1df">

- 전처리기(Preprocessor) : 헤더(#include), 매크로(#define) 등과 같은 전처리 지시자 해석
- 컴파일러(Compiler) : 소스코드를 어셈블리어(*.s) 형태로 변환
- 어셈블러(Assembler) : 어셈블리 코드를 기계어(Machine Code) 오브젝트 파일(*.o)로 변환
- 링커(Linker) : 생성된 목적 파일들을 묶고 라이브러리를 링킹하여 실행파일을 생성

## GCC 주요 옵션 정리
- `-o` : output 실행 파일 이름 지정
    - ex) gcc hello.c –o hello
- `-Wall` : 모든 경고 활성화 (경고 메시지)
    - ex) gcc –Wall hello.c –o hello
- `-E` : 전처리 과정 결과 생성
    - ex) gcc –E hello.c > hello.i
- `-S` : 어셈블리 코드 생성
    - ex) gcc –S hello.c > hello.s
- `-C` : 컴파일 코드 생성(링킹 없음)
    - ex) gcc –C hello.c
- `-save-temps` : 모든 컴파일 중간파일 생성
    - ex) # gcc -save-temps hello.c.s
- `-l` : 공유 라이브러리 링크
    - ex) gcc –Wall hello.c –o hello -lpthread
- `-v` : 모든 실행 커맨드 출력
    - ex) gcc –Wall –v hello.c –o hello

