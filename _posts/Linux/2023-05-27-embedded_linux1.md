---
layout: single
title:  "[직무교육] 임베디드 리눅스 커널 포팅 및 구조 Day1"
excerpt: "임베디드 리눅스"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 임베디드 리눅스 커널 포팅 및 구조

## Day1
### 개발환경 구성
- Vmware Workstation 17 Player
- Ubuntu14.04.05 vmwarevm 
  - VMware virtual machine config(.vmx) -> VMware에서 오픈
  - VMware virtual disk file
- 리눅스에서 인식할 수 있도록 XML 수정 -> device vid, pid 설정
- tftp directory만 target board에서 host에 접근할 수 있음
- 리눅스에서 파일명에 . 이 붙으면 숨김 파일임
- root 권한이 필요한 디렉토리에 접근 시 sudo 명령어 사용

### 임베디드 시스템 개발 환경
![임베디드 시스템 개발 환경](https://github.com/bellbpng/Embedded_Linux/assets/59792046/3c163ca0-edb1-47de-9a63-7ede245450a6)

- Host System
    - Target System 개발을 위한 개발환경 제공
    - Target System 개발을 위한 어셈블러, 컴파일러, 링커 등의 개발 도구 제공
- Target System
    - 개발하고자 하는 임베디드 시스템

### TFTP
- 임베디드 보드(Target)가 호스트로부터 이미지를 다운로드 할 때 사용하는 프로토콜
- UDP 기반으로 69번 Port 사용

### NFS (Network File System)
- 임베디드 리눅스 환경에서 스토리지(Disk)를 관리하는 프로토콜
- 네트워크를 통해 파일 시스템을 mount할 수 있게 해준다.
- 호스트의 Remote Storage에 저장해두고 사용하는 방법
- 외부에 있는 Disk 장치를 본인 것처럼 사용 가능
- 개발 단계에서 용이하지만, 네트워킹이 필요없는 제품의 경우 최종적으로는 타겟 보드의 NAND Flash에 모두 저장한다.

![NFS](https://github.com/bellbpng/Embedded_Linux/assets/59792046/bd4650c1-a8a4-4c4b-9645-25d377d60090)

### Toolchain
- 타겟 시스템에서 실행할 프로그램 개발을 위해 소스코드를 컴파일하고 링킹하며 실행가능한 파일로 만들어주는 개발도구를 통칭
- 크로스컴파일러 제작을 위해 활용됨
- [툴체인(Toolchain)이란?](https://kkhipp.tistory.com/176)

![Toolchain](https://github.com/bellbpng/Embedded_Linux/assets/59792046/bfb5a612-0d59-4749-b1ff-92ea0ba2de26)

### Root File System
- 리눅스 동작에 필요한 기능과 시스템 초기화 및 주변 장치 제어를 위한 부팅 과정에 필요한 프로그램들과 자료를 관리하는 파일 시스템
- [루트 파일 시스템 구성](https://m.blog.naver.com/ibank97/221971880145)
![Linux kernel directory](https://github.com/bellbpng/Embedded_Linux/assets/59792046/e044a2b1-0c7a-42c9-9e8c-ae76ec6d6485)
