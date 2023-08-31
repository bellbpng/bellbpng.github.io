---
layout: single
title:  "GDB 디버깅"
excerpt: "Linux"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# GDB 디버깅

## Introduction
- GDB(GNU Debugger)는 GNU 소프트웨어 시스템을 위한 기본 디버거이다.
- 다양한 유닉스 기반의 시스템에서 동작하는 이식성 있는 디버거로 C, C++, Fortran 등 여러 프로그래밍 언어를 지원한다.
- 프로그램을 줄 단위로 실행하거나 특정 지점에서 멈추도록 할 수 있다.
- 프로그램 수행 중간에 각각의 변수에 어떤 값이 할당되어 있는지 확인할 수 있다.
- 원하는 값을 변수에 할당 후 결과를 확인할 수 있다.
- 컴파일 시 `-g` 옵션을 주어 디버깅 정보를 추가해야한다.

## 기능
- 정지점(Break Point) 설정
    - 지정한 지점에서 프로그램 실행을 일시적으로 중단할 수 있다.
    - 정지점에서 프로그램을 중단한 뒤 특정 값을 표시하고 확인할 수 있다.
    - 조건을 주어 정지점을 설정할 수도 있다.
- 하드웨어 감시점
    - 일부 프로세서에서는 하드웨어를 사용하여 특정 메모리 값이 변하는지 감시할 수 있다.
- 프로그램 값과 속성 표시
    - 프로그램을 실행하면서 gdb 명령으로 현재 변수 값을 표시할 수 있다.
- 프로그램 단계별 수행
    - 실행 프로그램의 각 행을 한 번에 하나씩 실행할 수 있다.
- 스택 프레임
    - 프로그램에서 함수를 호출할 때마다 호출 관련 정보가 스택 프레임(stack frame) 또는 프레임(frame)이라는 자료 블록에 저장된다.
    - 함수 호출 하나당 프레임은 하나이다.
    - 프레임에는 함수에 전달된 매개변수, 함수의 지역변수 및 실행주소 등이 담겨있다.
    - 모든 스택 프레임은 콜 스택(call stack)이라는 메모리 영역에 할당된다.
    - `frame`, `info frame`, `backtrace` 등의 명령이 있다.

## GDB 명령

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/ca93c54a-45c0-49c5-ac64-5a767790af6e">

[출처: 패스트캠퍼스]


## 코어 덤프(core dump)
- 컴퓨터 프로그램이 특정 시점에 작업 중이던 메모리 상태를 기록한 것으로, 보통 프로그램이 비정상적으로 종료되었을 때 만들어진다.
- 프로그램 카운터, 스택 포인터 등 CPU 레지스터나 메모리 관리 정보, 프로세서 및 운영 체제 플래그 및 정보 등이 포함된다.
- 프로그램 오류 진단과 디버깅에 사용된다.
- 사용자 응용 프로그램이 문제가 생겼을 경우 코어(core) 파일을 생성할 수 있으려면, 시스템에서 제한하는 코어 파일 크기가 0보다 커야 한다.
- `ulimit -a` 명령으로 코어 파일 크기를 확인할 수 있고, `ulimit -c` 명령으로 코어 파일 크기를 변경할 수 있다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/bf51a2dc-6272-49fb-98f6-8f3b6898e7a6" width="600">

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/6a378495-a37e-4601-b60f-116d83397da7" width="600">

- 코어 덤프의 네이밍 형식은 다음과 같다.
    - `%e` - executable filename
    - `%p` - PID of dumped process
    - `%h` - hostname (same as nodename returned by unname(2))
    - `%t` - time of dump (seconds since 0:00h, 1, Jan 1970)

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/62b34485-4715-445c-b576-130e5d9e926e" width="600">

- **gdb_test1** : 실행 파일 이름
- **1566** : 비정상적으로 종료된 프로세스의 PID
- **localhost.localdomain** : hostname
- **1693444800** : time

- `gdb [소스파일]` 명령으로 오류 정보를 바로 출력하면서 확인할 수 있다.
- `run` 을 이용하여 실행

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/033e78c7-6433-411f-bc74-f25ce2635e54" width="700">

- `gdb [코어 덤프 파일]` 명령으로 생성된 코어 덤프 파일 내용을 위와 같이 출력할 수 있다.

## backtrace
- 코어 덤프 파일을 남길 수 없는 환경에서 segfault 발생, SIGKILL 시그널 수신 등 문제점이 발생한 상황이거나 시스템 진단 등의 목적으로 의도적으로 backtrace를 남겨 활용할 수 있다.
- `addr2line` 명령어로 기계어 명령 주소(16진수 형태)를 명령이 시작된 파일의 행에 매핑할 수 있다.

```c++
#include <execinfo.h>

int backtrace(void** buffer, int size);
```
- 포인터 목록으로 현재 스레드에 대한 backtrace 정보를 가져와 버퍼에 넣는다.
- size는 버퍼 크기 이상이어야 한다.
- 반환값은 확보된 버퍼의 실제 항목 수이다.
- 버퍼 내의 포인터들은 실제로 스택을 검사하여 얻은 반환 주소이며, 스택 프레임 당 하나의 반환주소이다.

```c++
char **backtrace_symbols (void *const *buffer, int size);
```
- backtrace_symbols 함수는 backtrace 함수에서 얻은 정보를 문자열 배열로 변환한다.
- buffer는 backtrace 함수를 통해 얻은 주소 배열에 대한 포인터 여야하며 size는 해당 배열의 항목 수이다.
- 반환값은 배열 버퍼와 마찬가지로 크기 항목이 있는 문자열 배열에 대한 포인터이다.
- 각 문자열에서 함수 이름, 함수에 대한 오프셋, 실제 리턴 주소(16진)가 포함된다..
- 함수 이름을 프로그램에서 사용할 수 있도록 링커에게 추가 플래그를 전달해야 한다. (`-rdynamic`)
- backtrace_symbols의 반환값은 malloc 함수를 통해 얻은 포인터이며 해당 포인터를 해제하는 것은 호출자의 책임이다.
- 개별 문자열이 아닌 반환 값만 해제해야 한다.
- 문자열에 충분한 메모리를 확보할 수 없는 경우 리턴값은 NULL 이다.

```c++
void backtrace_symbols_fd (void *const *buffer, int size, int fd);
```
- backtrace_symbols_fd 함수는 backtrace_symbols 함수와 동일한 변환을 수행한다.
- 호출자에게 문자열을 반환하는 대신 문자열을 파일 디스크립터 fd에 한 줄에 하나씩 쓴다.
- malloc 기능을 사용하지 않으므로 해당 기능이 실패할 수 있는 상황에서 사용할 수 있다.

### 예제
```c++
#include <execinfo.h>
#include <stdio.h>
 include <stdlib.h>
/* Obtain a backtrace and print it to stdout. */
void print_trace (void)
{
    void *array[10];
    size_t size;
    char **strings;
    size_t i;
    size = backtrace (array, 10);
    strings = backtrace_symbols (array, size);
    printf ("Obtained %d stack frames.\n", size);
    for (i = 0; i < size; i++)
    printf ("%s\n", strings[i]);
    free (strings);
}
    /* A dummy function to make the backtrace more interesting. */
void dummy_function (void)
{
    print_trace ();
}
int main (void)
{
    dummy_function ();
    return 0;
}
```
- 코드 실행 모습

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/f4a9fa2c-5304-4e1e-aaaa-720653dd60a9" witdh="700" height="300">

- 링커에게 backtrace를 사용한다는 플래그 `-rdynamic` 전달
- 5개의 스택 프레임 생성
    - 함수당 한개 이므로 main, print_trace, dummy_function 함수에 대해 총 3개
    - 기타 프로그램 실행에 필요한 정보로 2개 생성
- `addr2line` 명령어로 해당 주소가 소스코드에서 어디에 위치하는지 행단위로 파악할 수 있음

#### Reference.
- 패스트캠퍼스 리눅스 올인원 패키지 강좌