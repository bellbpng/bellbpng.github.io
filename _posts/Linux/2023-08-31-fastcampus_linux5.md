---
layout: single
title:  "파일 I/O (creat, access, stat, chmod, chown)"
excerpt: "Linux System Programming"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 파일 I/O (creat, access, stat)

## Introduction
- 파일은 리눅스 운영체제에서 가장 기본적이고 핵심이 되는 추상화 개념이다.
- 파일 디스크립터(File Descriptor)
    - 프로세스의 열린 파일을 고유하게 식별하는 정수 (fd)
- 파일 디스크립터 테이블(File Desctiptor Table)
    - 파일 테이블 엔트리(File Table Entry)들을 가리키는 포인터 요소이고, 이 파일 디스크립터를 가리키는 정수 배열의 집합을 말한다.
    - 운영체제에는 각 프로세스마다 하나의 고유한 파일 디스크립터 테이블이 제공된다.
- 파일 테이블 엔트리(File Table Entry)
    - 메모리 내에 존재하는 **열린** 파일에 대한 구조체
    - 파일을 열거나 파일 위치를 유지보수할 때 생성된다.
- 표준 파일 디스크립터(Standard File Descriptor)
    - 프로세스가 시작되면 해당 프로세스 파일 디스크립터 테이블의 `fd0`, `fd1`, `fd2` 가 자동으로 열린다.
    - 0(stdin) : 키보드에서 문자를 쓸 때마다 `fd0` 을 통해 stdin으로부터 읽어 들인다.
    - 1(stdout) : `fd1` 을 통해 화면의 stdout에 쓸 때마다 화면 출력으로 나타난다.
    - 2(stderr) : `fd2` 를 통해 화면의 stderr에 쓸 때마다 화면에 에러 출력으로 나타난다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/5462010c-cdbb-4a33-8666-6f6ac207cf24">

## 파일 생성 및 관리
### creat()
- 파일을 생성하기 위해 `creat()` 또는 `open()` 등을 이용

```c++
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int creat(const char *pathname, mode_t mode);
```
- `creat()` 함수는 이미 존재하는 파일의 경우 초기화 시키거나 존재하지 않는 파일 생성 시 사용
- pathbname 인자는 생성하거나 열고자 하는 파일 이름을 나타낸다.
- mode 인자는 파일 생성 시 파일의 권한(소유권)을 나타낸다.
- 정상 수행 시 파일 디스크립터(fd)를 반환하고, 에러 발생시 -1을 반환한다.

- **File mode bits**

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/afa15063-e1eb-4bda-a787-e448a89ba070">

[출처] 패스트캠퍼스

```c++
#include <stdio.h>
#include <fcntl.h> /* for mode_t */
#include <errno.h> /* for errno */
#include <string.h> /* for strerror */
int main()
{
    int fd;
    // User에게 read, write 권한을, Group과 기타사용자에게 read 권한을 준다.
    mode_t mode = S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH;
    char *filename = "/tmp/file"; // 파일 경로
    fd = creat(filename, mode);
    if (fd < 0) {   
        printf("creat error: %s\n", strerror(errno));
        return -1;
    }
    return 0
}
```

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/5f4060b3-3bb6-4fd8-a470-11bd635c3202" width="800">


### access()
- 호출 프로세스가 파일 경로 이름에 접근할 수 있는지 확인한다.

```c++
#include <unistd.h>

int access(const char *pathname, int mode);
```
- mode 인자는 수행할 접근성 검사를 지정한다.
    - `F_OK` : 파일 존재 여부
    - `R_OK` : 파일 존재 여부, 읽기 권한
    - `W_OK` : 파일 존재 여부, 쓰기 권한
    - `X_OK` : 파일 존재 여부, 실행 권한
- 정상 수행 시 0을 반환하고, 에러 발생 시 -1을 반환

```c++
#include <stdio.h>
#include <unistd.h>
int main()
{
    char *pathname = "./creat_example";
    int mode = R_OK | W_OK; // 파일 존재 여부와 읽기, 쓰기 권한을 확인
    if (access(pathname, mode) == 0) {
        printf("Read Write OK!\n");
    } 
    else {
        printf("You do not have permission or do not exist.\n");
    }
    return 0;
}
```

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/5d92ff66-2de3-4272-bea9-7fe5533c1a1b" width="800">

### stat()
- 파일의 메타 데이터 정보를 얻는 함수

```c++
#include <sys/types.h>
#include <sys/stat.h> // stat 구조체가 담긴 <bits/stat.h> 를 포함하는 헤더파일
#include <unistd.h>

// <bits/stat.h> 파일에 선언되어 있음
struct stat {
    dev_t st_dev; /* 파일을 포함하고 있는 장치 ID */
    ino_t st_ino; /* inode 번호 */
    mode_t st_mode; /* 권한 (permissions) */
    nlink_t st_nlink; /* 하드 링크 수 */
    uid_t st_uid; /* user ID */
    gid_t st_gid; /* group ID */
    dev_t st_rdev; /* device ID (특수 파일일 경우) */
    off_t st_size; /* 바이트 단위의 전체 사이즈 */
    blksize_t st_blksize; /* filesystem I/O 를 위한 블록 사이즈 */
    blkcnt_t st_blocks; /* 할당된 블록의 개수 */
    time_t st_atime; /* 마지막 접근 시간 */
    time_t st_mtime; /* 마지막 편집 시간 */
    time_t st_ctime; /* 마지막 상태 변경 시간 */
};

int stat(const char *path, struct stat *buf);
```

```c++
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <stdio.h>
int main(int argc, char *argv[])
{
    struct stat sb;
    int ret;
    if (argc < 2) {
        printf("Usage: %s <file>\n", argv[0]);
        return -1;
    }
    ret = stat(argv[1], &sb);
    if (ret) {
        perror("stat error: ");
        return -1;
    }
    printf("file(%s) is %ld bytes\n", argv[1], sb.st_size);
    return 0;
}
```
- main 함수로 전달되는 각 인자 `argc`, `argv[]` 는 다음과 같은 역할을 한다.
- `int argc` : main 함수로 전달되는 인자의 개수
    - default 는 1이다. (프로그램 실행 경로가 항상 전달되기 때문에)
- `char *argv[]` : main 함수에 전달되는 실질적인 정보로, 문자열 배열을 의미한다.
    - 첫번째 문자열은 프로그램의 실행 경로로 항상 고정이 되어 있다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/6e7ba3f2-9c17-4964-a5e2-722052bd2d01" width="800">

```c++
switch (sb.st_mode & S_IFMT) {
    case S_IFBLK:
        printf("block device node\n");
        break;
    case S_IFCHR:
        printf("character device node\n");
        break;
    case S_IFDIR:
        printf("directory\n");
        break;
    case S_IFIFO:
        printf("FIFO\n");
        break;
    case S_IFLNK:
        printf("symbolic link\n");
        break;
    case S_IFREG:
        printf("regular file\n");
        break;
    case S_IFSOCK:
        printf("socket\n");
        break;
    default:
        printf("unknown\n");
        break;
}
```
- 위와 같이 `st_mode` 와 `S_IFMT` 비트연산시 파일의 유형을 파악할 수 있다.

### chmod()
```c++
#include <sys/stat.h>

int chmod(const char *pathname, mode_t mode);
```
- pathname 의 모드를 변경한다.
- 정상 수행 시 0을 반환하고, 에러 발생 시 -1을 반환한다.

```c++
#include <stdio.h>
#include <unistd.h>

int main()
{
    char *filename = "./creat_example";
    int mode = F_OK;
    if (access(filename, mode) == 0) {
        // 사용자와 그룹에게 읽기,쓰기,실행 권한을 준다.
        if (chmod(filename, S_IRWXU|S_IRWXG) != 0) { 
            printf("chmod() error\n");
            return -1;
        }
    } 
    else {
        printf("file(%s) access error\n", filename);
        return -1;
    }
    return 0;
}
```

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/5491f7c2-3dbc-4ad8-bde0-b8dac4835ff8" width="800">

### chown()
```c++
#include <unistd.h>

int chown(const char *pathname, uid_t owner, gid_t group);
```
- pathname 의 소유권을 변경한다.
- 정상 수행 시 0을 반환하고, 에러 발생 시 -1을 반환한다.
- 소유권 정수값은 다음과 같다.
    - 0: root
    - 1: bin
    - 2: daemon

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/6d93484e-b589-4b43-846c-9ea94ec992d8" width="800">


```c++
#include <stdio.h>
#include <unistd.h>
int main()
{
    char *filename = "./creat_example";
    int mode = F_OK;
    if (access(filename, mode) == 0) {
        // 사용자는 bin, 그룹은 daemon으로 변경
        if (chown(filename, 1, 2) != 0) {
            printf("chown() error\n");
            return -1;
        }
    } 
    else {
        printf("file(%s) access error\n", filename);
        return -1;
    }
    return 0;
}
```

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/adb7a902-f92c-49b9-a5f1-a809d7fe1a87" width="800">


#### Reference.
- 패스트캠퍼스 리눅스 올인원 패키지 강좌