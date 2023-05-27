---
layout: single
title:  "[직무교육] 임베디드 리눅스 커널 포팅 및 구조 Day2"
excerpt: "임베디드 리눅스"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 임베디드 리눅스 커널 포팅 및 구조

## Day2
### Bootloader
- 운영체제가 시동되기 이전에 미리 실행되는 프로그램 (운영체제 시동 목적)
- 하드디스크이 첫번째 섹터에 위치
- U-BOOT(임베디드 리눅스), NTLDR(Window), GRUB(PC 리눅스)

### BSP(Board Support Package)
- 부트로더 소스 및 이미지 파일
- 리눅스 커널 소스, 디바이스 드라이버 소스 및 이미지 파일
- 램디스크 이미지 파일
- Cross Compiler Tool Package(gcc)

### U-Boot 소스코드 분석
- start_armboot(void) => main 함수
- 어셈블리 코드(.s)는 하드웨어 초기화도 하지만, C언어 소스코드를 호출해서 부팅을 진행할 수도 있다.

### Makefile
- 규모가 큰 프로그램의 경우 여러 개의 모듈로 나누어 프로그램을 개발하게 된다.
- 이 때, 입력 파일이 바뀌면 바뀐 파일과 관계 있는 다른 파일들이 다시 컴파일이 되어야 하는데, 이러한 과정을 쉽게 할 수 있도록 해주는 파일이다.
- [Makefile이 뭘까?](https://velog.io/@woodstock1993/Makefile)


### Vector Table
- 프로그램 맨 시작부분에 위치하여 프로그램이 동작 중에 문제가 발생하거나 인터럽트가 걸렸을 때 점프하는 부분이다.
- [Vector Table](https://velog.io/@audgus47/%EB%B2%A1%ED%84%B0-%ED%85%8C%EC%9D%B4%EB%B8%94Vector-Table)

### Porting
- 호스트에서 제작한 프로그램을 타겟 보드에서 실행될 수 있게 만들기 위해 요구되는 작업
- SoC 포팅
  - Reference 보드의 SoC와 target 보드의 SoC 차이점을 파악하고 소스코드에 반영하는 작업

### U-boot 포팅 실습
- 삼성에서 제공하는 smdk2450 용 부트로더를 Reference로 활용한다.
- 컴파일러를 수정하고 mds2450 보드용 디렉터리를 추가한다.
- mds2450과 유사한 시스템 보드의 디렉터리와 configuration 파일을 이용하여 mds2450에 맞도록 수정한다.
- 리눅스 커널을 메모리에 올리기 전이므로 U-boot가 메모리에서 차지하는 위치는 테스트 시에는 상관이 없으나, 리눅스 커널 자리를 미리 확보해두고 U-boot 위치를 정하는 것이 좋다.
- DRAM (64MB) 메모리 주소 범위
  - 0x30000000 ~ 0x33ffffff

![image](https://github.com/bellbpng/Embedded_Linux/assets/59792046/e98ab4f9-dd24-49e4-b987-b633a13ba57f)


### MMU(Memory Management Unit)
- 가상 메모리 주소 -> 물리 메모리 주소 변환
- 메모리 보호

### 커널 이미지 구조
- zImage: 압축된 형태
- 압축을 풀기 위한 head.S, 커널 시작을 위한 head.S 존재

![커널 이미지](https://github.com/bellbpng/Embedded_Linux/assets/59792046/0d5e253e-a51f-43b9-92b0-dde68d3d9b16)
