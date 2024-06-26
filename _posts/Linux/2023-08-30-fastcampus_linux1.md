---
layout: single
title:  "리눅스 개발환경 구축 "
excerpt: "Linux"

categories:
  - Linux
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 리눅스 개발환경 구축하기

## 가상머신 구축(Virutal Machine)
- 가상환경(Virtual Machine)이란
    - 하이퍼바이저(Hypervisor) 호스트 컴퓨터에서 다수의 운영체제를 동시에 실행하기 위한 논리적 플랫폼을 말한다.
    - 하이버파이저는 가상 머신을 생성하고 실행하는 프로세스이다. 메모리 및 처리와 같은 단일 호스트 컴퓨터의 리소스를 가상으로 공유하여 호스트 컴퓨터가 여러 가상 머신을 지원할 수 있도록 한다.
- 👉 [VirtualBox 설치 링크](https://www.virtualbox.org/wiki/Downloads)

## 운영체제 iso 파일 다운로드
- Linux 운영체제 중 필요에 맞는 운영체제를 설치한다.
- **Ubuntu, CentOS**
- mirroring 홈페이지를 이용한다. (Ubuntu)
    - 👉 [카이스트 미러](http://kr.archive.ubuntu.com/ubuntu/)
    - 👉 [카카오 미러](http://mirror.kakao.com/ubuntu-releases/xenial/)
    - 👉 [네오위즈 미러](http://ftp.neowiz.com/ubuntu-releases/)
- 설치한 이미지 파일을 저장소의 컨트롤러 IDE에 추가한다.
- CentOS는 공식 홈페이지를 활용한다.
    - CentOS는 GUI가 Ubuntu Desktop만큼 친절하지 않다.
    - 서버 개발에 많이 사용되는 운영체제

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/d9bd608c-72f4-4ad8-a978-9c39e4664f09">

## Ubuntu 버전 설명
- Ubuntu 버전
    - 프로젝트명
        - Trusty(14.xx) → Xenial(16.xx) → Bionic(18.xx) → Eoan(19.xx)
    - {Major Version}.{Minor Version}.{Path Version}
    - 홀수 버전: 최신 기능 (플래그쉽)
    - 짝수 버전: 안정성
    - LTS : Long-Term Support
        - 최초 릴리즈부터 최소 5년 보안 지원


## VirtualBox 네트워크 설정
- `NAT`
    - 가상머신 내부 네트워크에서 Host PC 외부 네트워크 단방향 연결
        - Host NIC -> 가상 NIC로 통신 불가능
    - Host 내부 네트워크와 통신 불가
- `어댑터에 브리지`
    - 호스트 PC와 동등하게 외부 네트워크와 연결
    - IP할당을 외부로부터 받음
- `내부 네트워크`
    - HOST 내부 네트워크와만 통신 가능
- `호스트 전용`
    - Host와 내부 네트워크와만 통신 가능
    - 외부 네트워크와 단절
- `일반 드라이버`- 거의 미사용
- `NAT 네트워크`
    - NAT + Host 내부 네트워크와 통신 가능

## PuTTY 설치 및 원격 접속
- PuTTY(퍼티, /ˈpʌti/[3])라고 부르며 SSH, Telnet, rlogin, raw TCP를 위한 클라이언트로 동작하는 자유 및 오픈 소스 단말 에뮬레이터 응용 프로그램이다.
- 👉 [PuTTY 설치 링크](https://www.putty.org/)
- 설치된 CentOS 혹은 Ubuntu 환경에서 사용자 로그인 후 `ifconfig` 명령어를 통해 IP 주소를 확인한다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/a9d4d230-3f42-4440-9cf1-29219e9bc1de">

- CentOS 에서 확인한 가상 NIC 장치명은 enp0s3 이고, IP주소는 **10.0.2.15** 임을 알 수 있음
- lo는 Loopback 처리를 위한 가상 인터페이스 장치명
- VirtualBox의 설정에서 **포트포워딩** 작업으로 SSH 원격 접속 가능
- 이때, NAT 환경을 사용하고 있기 때문에 [호스트 NIC -> Virtual Box 가상 NIC] 로 접근은 불가능하다.
- 즉, `10.0.2.15` 주소를 직접 사용할 수 없다.
- 따라서 현재 사용중인 `localhost` IP주소로 접근하여 포트 포워딩일 해줘야 한다.
- SSH는 22번 포트를 사용한다.
- HOST의 22번 포트로 들어오는 데이터는 가상머신의 22번 포트로 연결된다는 의미이다.

<img src="https://github.com/bellbpng/Baekjoon_hub/assets/59792046/89289642-59ac-42ce-8fee-c6a2970e8251">

#### Reference.
- 패스트캠퍼스 리눅스 올인원 패키지 강좌
