---
layout: single
title:  "디바이스 드라이버를 커널에 동적으로 올리는 방법"
excerpt: "Linux System Programming"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---


## 1. 장치파일 생성(mknod)
- **mknod**: 장치파일 생성 명령어

```bash
mknod ([옵션]) [장치명] [타입] [주번호] [부번호]

# example - char 형 장치파일 생성
mknod /dev/gpio c 230 0

# 장치명: /dev/gpio
# 루트파일시스템 포맷
```
- 장치명
  - 생성할 장치파일의 위치와 장치파일명
- 주번호
  - 커널에서 디바이스 드라이버를 구분하고 연결하는데 사용된다. 즉, 주번호는 제어하려는 디바이스를 구분하기 위한 디바이스의 ID 정도로 생각하면 된다.
- 부번호
  - 디바이스 드라이버 내에서 장치를 구분하기 위해 사용된다. 즉, 같은 종류의 디바이스가 여러 개 있을 때 그 중 하나를 선택하기 위해 사용된다.

## 2. 작성한 디바이스 드라이버 파일(object)을 리눅스 커널에 삽입(insmod)
- **insmod**: 모듈을 리눅스 커널에 삽입하는 명령어 

```bash
insmod [파일명] ([옵션])

# example
insmod gpio.ko

# 파일명: gpio.ko
# 해당 쉘을 실행하는 쉘 스크립트 경로 기준
```
- 해당 명령어를 실행하면 디바이스 드라이버 파일 내부 **module_init()** 함수가 실행된다.

## 3. 커널 내부에 장치 등록
```c
int register_chdev(unsigned int major, const char* name, struct file_operations* fpos);

// example
register_chdev(230, "gpio", )
```
- 모듈 적재 시 실행되는 **module_init()** 함수 내부에서 실행되는 함수로 커널 내부에 등록된 문자장치를 관리하는 `chrdev[]` 배열 구조체에서 하나의 배열을 할당 받고, 그 배열 안의 필드에 문자장치의 이름과 파일 오퍼레이션을 연결하는 역할을 한다.
- 1번 작업으로 만들어 둔 장치파일과 연결된다.

### ioctl() 디바이스 제어
- 저수준 파일 입출력 함수로써 디바이스 파일에 적용시키면 디바이스 파일에 연결된 디바이스 드라이버의 **file_operations** 구조체의 ioctl 필드에 선언된 함수가 호출된다.
- read(), write() 함수와 같이 정형화된 형태를 기본적으로 유지하지만, 사용 방법은 디바이스마다 모두 다르다.
- read(), write()로 다루지 않는 특수한 제어를 한다.
- 응용 프로그램에서 목적에 맞는 디바이스 제어를 위한 시스템 함수를 구현하기 위해 사용한다.

## 4. 응용프로그램에서 장치파일 열기
- 1번 과정에서 만들어 둔 장치파일을 **open()** 함수로 열어서 file descriptor 를 받는다.
- **file_operations** 에 등록된 시스템 함수를 호출하여 디바이스를 제어할 수 있다.


## 그림으로 정리

![image](https://github.com/bellbpng/Audio_preprocessor/assets/59792046/91ae97cf-556c-4c8d-af79-b22968367b0a)

### Reference.
[우진샘 블로그](https://naito.tistory.com/entry/%EB%A6%AC%EB%88%85%EC%8A%A4%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B2%84-%EA%B4%80%EB%A0%A8-%ED%95%A8%EC%88%98)

[I Will be Great Software](https://et-0.tistory.com/entry/Linux-%EC%9E%A5%EC%B9%98%ED%8C%8C%EC%9D%BC-%EC%A7%81%EC%A0%91-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0mknod)

[BlankSpace](https://blankspace-dev.tistory.com/287)

