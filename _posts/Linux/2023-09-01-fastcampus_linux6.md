---
layout: single
title:  "파일 I/O (open, close, fcntl, read, write, lseek)"
excerpt: "Linux System Programming"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 파일 I/O (open, close, fcntl, read, write, lseek)

## 파일 열고 닫기
### open()
```c++
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);
```
- 시스템 호출로 파일을 열거나 생성할 때 사용한다.
- 성공하면 해당 파일을 지시하는 int 형 파일 디스크립터(fd)를 반환하고, 에러 발생 시 -1을 반환한다.
- flag는 파일을 어떠한 모드로 열 것인지를 혹은 파일을 생성할 것인지를 결정하기 위해서 사용한다.
    - `O_RDONLY`: 읽기전용
    - `O_WRONLY`: 쓰기전용 
    - `O_RDWR`: 읽기/쓰기
    - `O_CREAT`: 파일 생성
- mode 인자가 붙은 형식과 붙지 않은 형식 둘 다 유효하다.
- mode 인자는 파일 생성 시 파일의 권한(소유권)을 나타낸다.
- 파일을 생성(`O_CREAT`)하지 않으면 mode 인자는 무시된다.
- 파일을 생성한다면 mode 인자로 파일 권한을 정의해주는 것이 좋다.

<img src = "https://github.com/bellbpng/Baekjoon_hub/assets/59792046/e77cadbd-815f-4311-87ee-d08e066d28b5">

<img src = "https://github.com/bellbpng/Baekjoon_hub/assets/59792046/9bbac066-6bd7-4203-b08c-4d6af41a4d18">

[출처 패스트캠퍼스]


### close()
```c++
#include <unistd.h>

int close(int fd);
```
- 파일 디스크립터(fd)를 닫아서 더 이상 파일을 참조하지 않고 재사용 할 수 있도록 한다.
- 프로세스와 연관되고 프로세스가 소유한 파일에 보유된 모든 레코드 잠금이 제거된다.
- fd가 마지막 파일 디스크립터인 경우 연관된 자원이 모두 해제된다.
- fd가 unlink를 사용하여 제거된 파일에 대한 마지막 참조인 경우 파일이 삭제된다.
- 정상 수행 시 fd 를 반환, 에러 발생시 -1 반환

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/dded411a-a5ba-42e6-b40d-5c1195fcd014">

[출처 패스트캠퍼스]


### fcntl()
```c++
#include <unistd.h>
#include <fcntl.h>

int fcntl(int fd, int cmd, ... /* arg */ )
```
- 파일 디스크립터(fd)를 조작한다.
- 파일 디스크립터(fd)에 대해 cmd에 의해 결정되는 작업을 수행한다.
- 추가 인수의 필요 여부는 cmd에 의해 결정되고, 인수가 필요하지 않은 경우 void가 지정된다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/8a970068-e900-48d2-a3f8-728e3141d457">

[출처 패스트캠퍼스]


## 파일 읽고 쓰기
### read()
```c++
#include <unistd.h>

ssize_t read(int fd, void *buf, size_t count);
```
- socket() 이나 open() 등으로 열린 파일 디스크립터(fd)에서 데이터를 읽어 들인다.
- fd 에서 읽을 데이터가 있다면 buf에 담는다.
- count는 buf로 한번에 가져올 데이터의 크기를 의미한다.
- 성공할 경우 읽어들인 buf의 크기를 반환한다. (0이상)
- 0이라면 파일의 끝을 의미하며, 데이터를 가져오는데 성공했다면 파일 포인터의 위치는 읽은 데이터 크기만큼 이동한다.
- 에러가 발생할 경우 -1을 반환한다.

### write()
```c++
#include <unistd.h>

ssize_t write(int fd, const void *buf, size_t count);
```
- open() 이나 socket() 등으로 열린 파일 디스크립터가 가리키는 파일에 buf에 담긴 데이터를 쓴다.
- count는 쓸 데이터의 크기이다.
- 성공할 경우 쓰여진 바이트 크기만큼을 반환한다.
- 0이면 쓰여진 것이 없음을 나타내고, 에러인 경우 -1을 반환한다.

### lseek()
```c++
#include <sys/types.h>
#include <unistd.h>

off_t leek(int fd, off_t offset, int whence);
```
- 파일 디스크립터(fd)의 위치 포인터를 offset만큼 이동하여 위치를 변경한다. 
- whence는 offset을 더해줄 기준점을 정한다.
    - `SEEK_SET` : 파일의 처음을 기준
    - `SEEK_CUR` : 파일의 현재 위치를 기준
    - `SEEK_END` : 파일의 마지막을 기준
- 성공했을 경우 **파일의 시작**으로부터 멀어진 바이트만큼의 offset을 반환한다.
- 실패했을 경우 -1을 반환한다. 

## 실습 프로그램
- 데이터가 저장된 파일을 열어 특정 부분의 데이터 수정을 시도한다.
- 수정하는 동안 수정중인 데이터 부분에 lock을 걸어 다른 프로세스가 참조할 수 없도록 한다.

### code
```c++
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

#define LOCK_T 0
#define UNLOCK_T 1
#define BUFSIZE 1024

int record_lock(int type, int fd, int start, int len);

int main(int argc, char **argv)
{
	int fd;
	int record_start, record_len;
	char buf[BUFSIZE] = {0};
	int i;

	// argument 4개: 컴파일된 바이너리 파일, record file, record 시작위치, 수정할 영역의 길이
	if (argc < 4) {
		printf("Usage: %s [record file] [record start] [record length]\n", argv[0]);
		exit(0);
	}

	// record file open (read/write 권한)
	fd = open(argv[1], O_RDWR);
	if (fd == -1) {
		perror("file open error: ");
		exit(0);
	}

	// 2번, 3번째 인자를 정수형태로 저장
	record_start = atoi(argv[2]);
	record_len = atoi(argv[3]);
	if (record_len > BUFSIZE) {
		printf("record_len(%d) cannot over %d\n", record_len, BUFSIZE);
		exit(0);
	}

	/* record lock */
	// start 부분부터 record_len 길이만큼 lock을 검
	if (record_lock(LOCK_T, fd, record_start, record_len) == -1) {
		perror("record lock error: ");
		exit(0);
	}

	/* process data */
	// fd 내의 위치 포인터 이동
	lseek(fd, record_start, SEEK_SET);
	if (read(fd, buf, record_len) < 0) {
		perror("read error: ");
		exit(0);
	}
	printf("record data = %s\n", buf);

	/* data modify */
	for (i = 0; i < record_len; i++) {
		if (buf[i] == '0' || buf[i] == '9')
			buf[i] = 'x';
	}
	// record_start로 포인터 이동
	lseek(fd, record_start, SEEK_SET);
	write(fd, buf, record_len);

	/* delay 20 sec */
	// lock 획득 실패 확인 (두번째 프로그램을 20초안에 실행 후 확인)
	sleep(20);
	printf("record lock process done\n");

	/* record unlock */
	if (record_lock(UNLOCK_T, fd, record_start, record_len) == -1) {
		perror("record unlock error: ");
		exit(0);
	}

	close(fd);

	return 0;
}

int record_lock(int type, int fd, int start, int len)
{
	int ret;
	struct flock lock; //lock config

	// write lock or unlock
	lock.l_type = (type == LOCK_T) ? F_WRLCK : F_UNLCK;
	// lock start point
	lock.l_start = start;
	// 파일의 시작부터 시작해서 offset(start) 부터 len까지 영역
	lock.l_whence = SEEK_SET;
	lock.l_len = len;

	ret = fcntl(fd, F_SETLK, &lock);
	return ret;
}
```
- 프로그램 실행 시 `&` 를 마지막에 붙이면 백그라운드 실행이 가능

`./record_lock_test data_file.txt 11 10 &`


#### Reference.
- 패스트캠퍼스 리눅스 올인원 패키지 강좌