---
layout: single
title:  "[직무교육] 임베디드 리눅스 커널 포팅 및 구조 Day3"
excerpt: "임베디드 리눅스"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 임베디드 리눅스 커널 포팅 및 구조

## Day3
### 리눅스 커널 로드 단계
![리눅스 커널 로드](https://github.com/bellbpng/Embedded_Linux/assets/59792046/49edd3f1-4999-419d-9c2c-13288084b15d)

- 커널이 시작되면서 페이지 테이블이 만들어진다.
- MMU가 설정되면 리눅스 커널은 가상주소를 기준으로 메모리 맵을 사용한다.
- Exception Init 단계에서 리눅스에서 사용되는 exception vector와 handler가 복사된다.

### 부팅 과정 이해
1. Linux Kernel Start
2. SVC 모드 전환 및 인터럽트 중지
3. 프로세서 정보 검색
4. 머신 아키텍처 정보 검색
5. 페이지 테이블 설정
6. MMU 및 Cache 설정
7. Stack 및 시스템 정보 설정
8. BSS clear
9. 시스템 정보 저장
10. start_kernel() 호출(C언어)
  - 리눅스 커널 부팅의 거의 모든 과정이 이루어짐
- MMU에 의해 메모리 보호
  - 커널(SVC) 모드: 모든 메모리 접근 가능
  - 사용자(User) 모드: 허용된 메모리만 접근 가능

### init kernel thread
- root file system mount
- /dev/console open
  - stdin
  - stdout
  - stderr

### 리눅스 디렉터리 구성
- /bin, /sbin, /usr/bin, /usr/local/bin
  - 실행파일이 존재하는 공간
- /etc
  - 시스템 환경 정보가 존재하는 공간
- /root
  - root 사용자가 사용하는 기본 디렉터리
- /usr, /usr/local/lib, /usr/local/include
  - 윈도우의 Program files 디렉터리와 같은 역할
  - 설치 프로그램이 존재하는 공간
- /lib
  - 커널 모듈(kernel object)이 존재하는 공간
  - 기본 라이브러리 위치
- /boot
  - 부트로더가 위치하는 공간 (부트 섹터)
- /dev
  - 장치(하드웨어)를 다루기 위한 진입점 역할
  - devtmpfs : 사용되는 파일 시스템
- /proc
  - procfs: 사용되는 파일 시스템
  - 커널 정보가 존재하는 공간
- /sys
  - device driver 정보가 존재하는 공간. 상태 확인 가능
  - sysfs: 사용되난 피일 시스템
- /mnt
  - mount 된 파일들이 존재하는 공간

### 커널 빌드 방법
```
build_kernel clean
  # build 파일 삭제
  # config 파일도 삭제됨
  # make mrproper
```
``` 
./build_kernel config
  # 새롭게 config를 만들어주어야 함
  # make menuconfig
```
```
./build_kernel all /tftpboot
```
```
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- zImage modules
```
- module을 만드는 정식 명령어
- zImage 는 /arch/arm/boot/ 에 만들어짐
```
make INSTALL_MOD_PATH=$SYSROOT1 modules_install
```
- root 폴더에 .ko를 복사하여 붙여넣기

### 커널 포팅 실습
1. 리눅스 커널 소스를 다운받고 압축을 해제한다.
2. linux-3.0.22/Makefile 수정
  - ARCH와 CROSS_COMPILE 변수 수정
  - CROSS_COMPILE 변수는 해당 툴체인의 cross prefix에 알맞게 지정
3. 타겟 보드와 유사한 보드 선택 후 수정
  - 표준 커널에 포함된 S3C2416 기반 보드들 가운데 SMDK2416 보드가 MDS2450 보드와 가장 유사
  - smdk2416 설정 로드 후 mds2450 보드 관련 설정으로 수정
```
make s3c2410_defconfig
cd arch/arm/mach-s3c2416
cp mach-smdk2416.c mach-mds2450.c
```
4. mach-mds2450.c 편집
  - smdk2416 문자열(함수명, 변수)을 모두 mds2450으로 수정
  - MACHINE_START(MDS2450, "MDS2450") 수정
5. arch number를 가지고 동작하도록 수정
  - arch/arm/tools/mach-types에 추가
```
mds2450   MACH_MDS2450    MDS2450   3495
```
6. 커널 옵션 설정
- 타겟 보드 추가
- Kconfig는 Makefile과 연동되고, Makefile은 .config를 만들기위해 사용된다.
- Kconfig는 menuconfig에 어떻게 나타날지를 결정
- menuconfig에서 커널 옵션 설정 (제일 까다로움)

7. 커널 부트 테스트
- 빌드가 완료되면 arch/arm/boot/zImage가 생성되고, 생성된 파일을 /tftpboot로 복사한다.
```
cp arch/arm/boot/zImage /tftpboot
```
- Bootloader에서 새로운 커널 이미지를 로드하고, 부팅을 확인한다.
```
MDS2450# tftp 32000000 zImage;bootm 32000000
```

8. Device 설정
- 7번까지의 포팅 과정은 최소한으로 필수적인 것들만!
- 필요한 디바이스 드라이버를 포팅하는 단계