---
layout: single
title:  "[직무교육] 임베디드 리눅스 커널 포팅 및 구조 Day4"
excerpt: "임베디드 리눅스"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 임베디드 리눅스 커널 포팅 및 구조

## Day4
### 루트 파일 시스템
- 완전한 리눅스 시스템을 지원하기 위한 기본적인 파일 시스템 구조
- 오픈소스 프로그램이 설치되는 디폴트 경로
  - /usr
  - /usr/local
  - /opt

### 루트 파일 시스템 구축 실습
1. 환경변수 설정
```
 echo 'SYSROOT=/usr/local/arm-none-linux-gnueabi/arm-none-linux-gnueabi/sysroot/' >> ~/.bashrc
```
  - Day1에 제작한 툴체인(GCC 4.3.4) 하위 경로로 불완전한 루트 파일 시스템이다.
  - 툴체인이 기본 뼈대를 만들어준다고 생각하면 됨
2. 기존 rootfs 디렉터리를 복사하고 백업한다.
```
sudo cp -ra sysroot rootfs_new // 기존 루트 파일 시스템 백업
sudo mv sysroot rootfs.bak // 새로운 루트 파일 시스템 디렉터리
sudo ln -s rootfs_new sysroot
cd rootfs_new
```
3. 루트 파일 시스템 디렉터리 생성
- 필요한 디렉터리 생성(dev, sbin , ...)

4. Busybox 설치
- 임베디드 시스템용 필수 유틸리티 바이너리 패키지
- 심볼릭 링크 형태로 유틸리티들이 단일의 Busybox application으로 연결됨

5. SysV Init 설치
- **커널이 실행된 후** 가장 먼저 시작하는 시스템 초기화 프로그램
- 타겟 시스템의 시작 환경 설정을 유연하게 할 수 있다.
- Busybox가 지원하는 init 보다 성능이 더 좋음
- /etc/inittab 파일의 내용에 따라 순서대로 실행
- /etc/inittab 파일을 만들어 /sbin/init 에 대한 설정을 해야함
  - 까다로운 작업이라 기존에 안정적으로 동작하는 etc 디렉터리를 복사해서 사용
  - udev 설치(6번과정) 이후에 진행하는 것이 좋음
``` 
# /etc/inittab
#
# Copyright (C) 2001 Erik Andersen <andersen@codepoet.org>
#
# Note: BusyBox init doesn't support runlevels.  The runlevels field is
# completely ignored by BusyBox init. If you want runlevels, use
# sysvinit.
#
# Format for each entry: <id>:<runlevels>:<action>:<process>
#
# id        == tty to run on, or empty for /dev/console
# runlevels == ignored
# action    == one of sysinit, respawn, askfirst, wait, and once
# process   == program to run
id:3:initdefault:

# Startup the system
m1:23456:sysinit:/bin/mount -t proc proc /proc
# ::sysinit:/bin/mount -o remount,rw / # REMOUNT_ROOTFS_RW
m2:23456:sysinit:/bin/mkdir -p /dev/pts
m3:23456:sysinit:/bin/mkdir -p /dev/shm
m4:23456:sysinit:/bin/mount -a
m5:23456:sysinit:/bin/hostname -F /etc/hostname
# now run any rc scripts
m6::sysinit:/etc/init.d/rcS

# Put a getty on the serial port
#ttySAC1::respawn:/sbin/getty -l /sbin/autologin -L ttySAC1 115200 vt100
m7::respawn:/sbin/getty -L ttySAC1 115200 vt100

# Stuff to do for the 3-finger salute
m8::ctrlaltdel:/sbin/reboot

# Stuff to do before rebooting
#m9::shutdown:/etc/init.d/rcK
#m10::shutdown:/bin/umount -a -r
#m11::shutdown:/sbin/swapoff -a
```

6. udev 설치
- 하드웨어 장치를 연결하면 동적으로 device driver가 load 되도록 하는 프로그램

7. minicom root 파일 경로 설정
```
set bootargs 'root=/dev/nfs rw nfsroot=192.168.20.90:/usr/local/arm-none-linux-gnueabi/arm-none-linux-gnueabi/rootfs_new ip=192.168.20.246:192.168.20.90:192.168.20.1:255.255.255.0::eth0:off console=ttySAC1,115200n81'
```
- 위 명령을 minicom bootloader 환경에서 실행

### 시스템 패키징
- 리눅스 커널, Root File System을 고정 Storage에 넣어두는 방법
1. NAND flash initrd 패키징 모델 
  - zImage, Root file system(ramdisk.gz)을 NAND Flash에 write한다.
  - 커널이 부팅되면서 Root file system 압축을 풀어 RAM에 로드하는 방법
  - RAM에 file system이 올라가는 방식이기 때문에 전원이 종료되면 write한 내용이 모두 지워짐

![램디스크 패키징](https://github.com/bellbpng/Embedded_Linux/assets/59792046/be7c57ad-fdc9-4142-bb9c-fe357cb8e6ff)


2. MTD(jiff2 or yaffs2) 패키징 모델
  - 1번과 다르게 NAND Flash에 Root file system을 구축하는 방식
  - 속도가 1번에 비해 느리지만 NAND flash를 스토리지로 사용하기 때문에 데이터가 보존된다.
  - 실제 제품 출시에서는 메모리가 부족하기 때문에 목적에 맞게 루트 파일 시스템을 파티셔닝하여 관리한다.

![MTD 패키징](https://github.com/bellbpng/Embedded_Linux/assets/59792046/66ecd131-bc21-4134-9487-13411400e8b4)


3. MTD + Ramdisk 혼합 패키징 모델
  - 가장 많이 쓰임

![MTD+ramdisk 혼합 패키징](https://github.com/bellbpng/Embedded_Linux/assets/59792046/66011af2-ffc6-4926-9791-0dd962a10049)


### 임베디드 리눅스 시스템 최적화
- Root File System 디스크 용량 줄이기
1. 시스템 유틸리티 최적화
  - 임베디드 리눅스 시스템에서 꼭 필요한 유틸리티만 남긴다.
2. 부팅 절차 단순화
  - 불필요한 스크립트 실행을 막는다.
  - Shell 최적화
    - user의 입력을 모니터링 하면서 입력에 대해 반응해주는 역할
    - 용량이 임베디드 환경에서는 꽤 크다.
    - Busybox 에서는 ash 같은 임베디드용 쉘을 사용한다.
3. 라이브러리 최적화
  - glib: 리눅스에서 가장 최적화 된 라이브러리
  - newlibc, dietLibc, uClibc 등 glib에서 임베디드용으로 간소화 된 라이브러리
4. 커널 최적화
  - 모듈 활용
    - 커널에 포함되어 수행 가능하고, 동적으로 커널에 로딩되거나 제거될 수 있음
  - 필요 없는 기능 제거
  - 컴파일 옵션
  - **디버그 심볼** 정보 제거
    - **strip** 을 사용

5. 루트 파일 시스템 최적화
  - 정적 라이브러리는 컴파일 이후 삭제 가능
  - 동적 라이브러리는 반드시 필요
```
find -iname '*.a' -exec rm {} \;
rm -r usr/include/*
```
6. 최적화 검증
- Target을 NFS로 부팅되는지 확인한다.
- U-boot prompt에서 아래 명령어로 재설정
```
set bootargs 'root=/dev/nfs rw nfsroot=192.168.20.90:/nfs/rootfs ip=192.168.20.246:192.168.20.90:192.168.20.1:255.255.255.0::eth0:off console=ttySAC1,115200n81'
```

### 램디스크를 이용한 패키징 제작 실습
1. 램디스크로 사용할 파일 생성
  - 루트 파일 시스템을 타겟에 위치시키기 위해 사용
  - /dev/zero 파일
    - 내용이 모두 0이고 크기가 무한대
  - dd 명령어를 사용하여 이미지 생성
2. 파일시스템 생성
3. 램디스크 이미지 마운트
4. 루트 파일 시스템 복사
  - 생성한 루트 파일 시스템의 모든 내용을 램디스크에 복사
5. 마운트를 해제하고 이미지를 gzip으로 압축하여 /tftpboot 디렉터리로 복사
6. 커널 옵션 설정 및 빌드
7. 커널 이미지 및 램디스크 이미지 로드
  - 생성한 커널 이미지와 램디스크 이미지를 타겟 보드의 RAM에 로드
  - U-boot prompt에서 아래 명령어 실행
```
MDS2450# tftpboot 32000000 zImage
MDS2450# tftpboot 30800000 ramdisk-mds2450.gz
MDS2350# bootm 32000000
```
8. 커널 이미지 및 램디스크 이미지 NANd fusing
- 제작한 램디스크를 NAND flash에 write 하는 작업
- U-boot prompt에서 아래 명령어 실행
```
tftpboot 32000000 zImage;tftpboot 30800000 ramdisk-mds2450.gz
```
```
nand erase 00080000 00500000
nand write 32000000 00080000 00500000
```
```
nand erase 00600000 01000000
nand write 30800000 00600000 01000000
```
```
setenv bootcmd 'nand read 32000000 00080000 00500000;nand read 30800000 00600000 01000000;bootm 32000000'
```

---
## 알아두면 좋은 내용
### 백업 방법
1. Linux shut down
```
sudo shutdown -h now
```
2. 압축
- Ubuntu14.04.05 - 복사본.vmx
- Ubuntu14.04.05.vmx
- Ubuntu14.04.05-dis1.vmdk


#### 명령어
```
md5sum linux-kernelporting.tar.gz
  - 파일의 유효성 검사
```
```
cd / 
  - 최상위 디렉토리로 이동
  - 리눅스 최상위 디렉토리
```
```
tar zxf linux-kernelporting.tar.gz
tar jxf linux-gnu.tar.bz2
  - 압축 해제
  - 세미콜론(;)으로 명령문 구분 역할로 여러개 명령문 한번에 입력 가능
```
```
sudo mv arm-2010q1/ /usr/local/
  - arm-2010q1 폴더 경로 이동
```
```
ping 8.8.8.8
  - 네트워크 상태 점검
  - 구글 도메인(8.8.8.8)
```
```
grep -rni tftp /etc
  - /etc 하위경로에 tftp 문자열이 들어간 파일들을 대소문자 구분하지 않고 찾는다.
 ```
 ```
 sudo /etc/init.d/xinetd restart
  - Ubuntu14.04.05 애소 tftp 서버 시작
```
```
gcc -o hello main.c
  - gcc로 컴파일
```
```
subl '우분투 14 apt-get 안될때.txt'
  - 인용부호(') 사용하여 공백문자가 포함된 파일 편집하기
```
```
alias
  - 단축 명령어 만들기
```
```
ctrl+shift+c / ctrl+shift_v
  - 복사 / 붙여넣기
ctrl+L
  - 화면 지우기
ctrl+shift+N
  - 새 터미널 띄우기
```
```
subl ~/.bashrc
  - 문서 편집기 (bashrc 편집)
source ~/.bashrc
  - bashrc 편집 전 열었던 터미널에 수정사항 적용
h
  - 명령어 history 확인 가능
ctrl 누르고 커서 방향키 이동하면 이동속도가 빠름(단어단위 이동)
```
```
which ct-ng
  - ct-ng가 설치된 경로
```
```
cp -ra linux-3.0.22 linux-3.0.22_v1
  - 백업본 만들기
```
