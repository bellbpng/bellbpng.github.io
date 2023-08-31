---
layout: single
title:  "Make 및 Makefile"
excerpt: "Linux"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# Make 및 Makefile

## Make 란?
- 소프트웨어 개발을 위해 리눅스 운영 체제에서 주로 사용되는 프로그램 빌드 도구
- 여러 파일들끼리의 의존성과 각 파일에 필요한 명령을 정의함으로써 프로그램을 컴파일할 수 있으며 최종 프로그램을 만들 수 있는 과정을 서술할 수 있는 표준 문법이 존재
- 표준 문법으로 작성된 파일(**Makefile**)을 make가 해석하여 프로그램 빌드를 수행
- `make` 를 명령어로 실행하면 해당 디렉토리에 위치한 **makefile** 을 읽어서 내용을 수행

## Makefile 작성법
```makefile
target … : prerequisites …
		recipe
		…
		…
```
- `target`
	- 일반적으로 프로그램에 의해 생성되는 파일의 이름으로 실행 파일 또는 object 파일
- `prerequisites`
	- `target` 을 작성하기 위한 입력으로 사용되는 파일들
- `recipe`
	- 수행 동작
	- 모든 recipe 줄의 시작 부분에 탭 문자를 넣어야 한다.

### Simple Makefile
```makefile
edit : main.o kbd.o command.o display.o insert.o search.o files.o utils.o
		cc -o edit main.o kbd.o command.o display.o insert.o search.o files.o utils.o

main.o : main.c defs.h
		cc -c main.c
kbd.o : kbd.c defs.h command.h
		cc -c kbd.c
command.o : command.c defs.h command.h
		cc -c command.c
display.o : display.c defs.h buffer.h
		cc -c display.c
insert.o : insert.c defs.h buffer.h
		cc -c insert.c
search.o : search.c defs.h buffer.h
		cc -c search.c
files.o : files.c defs.h buffer.h command.h
		cc -c files.c
utils.o : utils.c defs.h
		cc -c utils.c
clean :
		rm edit main.o kbd.o command.o display.o \
		   insert.o search.o files.o utils.o
```
- `edit` 이라는 실행 파일을 만들기 위해서는 main.o, kbd.o 등 8개의 오브젝트 파일이 필요하고, 8개의 오브젝트 파일을 Linking 해서 얻는다.
	- 총 8개의 소스파일과 3개의 헤더파일에 의존한다.
-  `main.o` 오브젝트 파일을 만들기 위해서는 main.c, defs.h 파일들이 필요하고, main.c를 컴파일해서 얻는다.
	- 다른 오브젝트 파일들도 마찬가지로 진행된다.
- `clean` 은 레이블로써 바이너리 및 오브젝트 파일을 정리한다.

### Makefile 실사용 예시
```makefile
CFLAGS = -g –O2 -I../include
LDLIBS = -lpthread

TARGET = test_program
OBJS = main.o config.o debug.o log.o

all : $(OBJS)
		cc –o $(TARGET) $(OBJS) $(LDLIBS)
clean :
		rm –f $(OBJS)
		rm –f $(TARGET)
```
- `CFLAGS`, `LDLIBS`, `TARGET`, `OBJS` 등 매크로를 지정한다.
- 매크로를 지정해두면 `all : $(OBJS)` 와 같이 여러개의 오브젝트 파일들을 간단하게 표현할 수 있다.
- `make` 만 입력하면 암묵적으로 `all` 을 실행한다.
	- `make all` 와 같은 효과
- `make clean` 을입력하면 `clean` 레이블을 실행하고, 바이너리 및 오브젝트 파일을 정리한다.
- 매크로는 변수와 같이 이름을 선언하고 내용을 서수한다.
- **$(매크로명)** 을 이용하여 작성한 매크로를 사용할 수 있다.

### 주요 사전 정의 변수
- `CC`: C 컴파일러, 기본값 cc
- `CFLAGS`: 컴파일 옵션
	- `CFLAGS`=-g-O2-l../includes
- `LDFLAGS`: 컴파일러가 -L과 같은 링커 'ld'를 호출할 때 제공할 추가 플래그
	- `LDFLAGS` = -L. –L$(LIB_DIR)
- `LDLIBS` : 링커 'ld'를 호출할 때 컴파일러에 제공되는 라이브러리 플래그 또는 이름
	- -L 과 같은 비라이브러리 링커 플래그는 `LDFLAGS` 변수에 있어야 함
	- `LDLIBS`=-lpthread

### 주요 자동 변수
```makefile
CFLAGS = -Wall
TARGET = test_program
OBJS = main.o sub.o

all : $(TARGET)
$(TARGET) : $(OBJS)
		$(CC) $(CFLAGS) –o $@ $^
main.o : main.c
		$(CC) $(CFLAGS) –c –o $@ $^
sub.o : sub.c
		$(CC) $(CFLAGS) –c –o $@ $^
clean :
		rm –rf *.o $(TARGET)
```
- `$@` : 최종 타겟의 파일 이름
- `$<` : 첫번째 전제 조건의 이름
- `$?` : 현재 타겟 파일을 생성하는데 변경된 모든 전제 조건 파일들의 이름
- `$^` : 모든 전제 조건 파일들의 이름
- `$*` : 타겟 이름이 확장자로 끝나는 경우, 확장자를 뺀 이름
	- 타겟이름이 **foo.c** 인 경우 **foo** 로 설정

### 패턴 규칙
- `%.c` 패턴은 **.c** 로 끝나는 모든 파일 이름과 일치
- `s.$.c` 패턴은 **s.** 으로 시작하고 **.c** 로 끝나는 파일 이름과 일치
```makefile
%.o:%.c
		$(CC) $(CFLAGS) –c $< –o $@
```
- `.c` 파일을 `.o` 파일로 컴파일하는 규칙

### 치환 매크로
- $(매크로:변경전=변경후) -> 변경전이 변경후로 바뀌어 사용됨
```makefile
SRCS = main.c command.c display.c utils.c files.c
OBJS = main.o command.o display.o utils.o files.o
```

```makefile
SRCS = main.c command.c display.c utils.c files.c
OBJS = $(SRCS:.c=.o)
```

- 위 두 코드는 같은 결과를 보임

#### Reference.
- 패스트캠퍼스 리눅스 올인원 패키지 강좌
