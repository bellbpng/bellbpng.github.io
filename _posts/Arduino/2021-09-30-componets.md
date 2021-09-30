---
layout: single
title:  "Arduino starter kit 구성품"
excerpt: "components of Arduino starter kit"

categories:
  - Arduino
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# Introduction
- 현재(2021-2학기) 오스트리아 인스부르크로 교환학생을 와있는데, 같이 수업듣는 현지 학생이 Electronics, Embedded Software 에 관심이 많은 친구입니다. 이번에 같이 프로젝트를 진행하게 되어서 함께 연구실에서 다양한 업무를 보고 있는데, 관심있으면 공부해보라고 아두이노 키트를 선뜻 주었습니다. 적절한 시기에 좋은 기회가 될 것 같아 독학하는 내용을 블로그에 기록해놓을 생각입니다. 

# Arduino Starter Kit
- 아두이노 스타터 키트의 구성품을 알아봅시다!

<img src="https://user-images.githubusercontent.com/59792046/135444530-4f5faff1-18da-4b11-b91f-b60ba2401b0a.jpg" width = "500">

<img src="https://user-images.githubusercontent.com/59792046/135444537-bd0c85fd-3bf7-4a34-88bc-50baab93aeb5.jpg" width = "500">

<img src="https://user-images.githubusercontent.com/59792046/135445173-2eaa0edb-9f1f-4e7a-9edb-57dab6c96eb2.jpg" width = "500">

## Arduino Starter Kit datasheet
- datasheet를 통해 구성품을 문서로 확인할 수 있습니다.

- go to [Datasheet](https://cdn-reichelt.de/documents/datenblatt/A300/ARDUINO_STARTER.pdf)

## Arduino Uno
<img src="https://user-images.githubusercontent.com/59792046/135448183-df064d86-c4ec-4389-864a-8080f1bd7499.png" width = 500>

<img src="https://user-images.githubusercontent.com/59792046/135447830-4dbde767-ce0e-4241-934f-6d9464d093d5.PNG" width = 500>

- 마이크로컨트롤러로는 ATmega328P 라는 AVR 8-bit가 사용되었고, 이에 대한 datasheet 또한 첨부하였다. (go to [Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/ATmega48A-PA-88A-PA-168A-PA-328-P-DS-DS40002061A.pdf))

### 디지털 핀
- 0 ~ 13번핀, 총 14개를 사용하며 HIGH(5V) or LOW(0V) 이진신호를 입출력한다.
- 0,1번: 시리얼 통신(USB <-> PC, TX는 전송, RX는 수신)
- 2,3번: 인터럽트(Interrupt) 기능
- 3,5,6,9,10,11번: PWM 기능이 있어서 디지털 신호를 제어하여 아날로그 신호와 유사한 효과를 낼 수 있도록 출력한다. (~표시가 되어있는 핀이 PWM 핀)

### 아날로그 핀
- A0 ~ A6를 사용하여 외부의 아날로그 신호를 입력받을 수 있다. 일반 디지털 핀처럼 GPIO(General Purpose Input/Output) 기능도 제공하여 디지털 핀으로도 사용가능하다.
- 10비트로 이루어진 ADC(Analog to Digital Converter)로 입력되는 값을 0~1023의 디지털로 표시한다. 0V=0, 5V=1023, 5V보다 큰 전압은 1023으로만 표시된다. 

### 리셋
- 아두이노 프로그램을 재시작한다.

### 외부전원 소켓
- DC 배터리를 통해 전력을 공급받을 수 있다.

### USB 포트
- 컴퓨터와 연결하여 프로그램을 업로드하거나 전원을 공급받을 수 있다.

### 3.3V & 5V
- 출력전압의 크기

### GND
- 회로의 접지 역할

### AREF(Analog Reference)
- 아날로그 전압값을 읽어야할 때 더 정확하게 측정할 수 있도록 도와주는 역할을 한다.
- 아두이노 우노의 경우 5V를 기준전압으로 삼는다. 이 기준전압을 필요에 따라서 변경할 수 있는 핀이다.

### ICSP(In Circuit Serial Programming)
- MCU에 직접 프로그래밍 가능한 통신포트이다.
- SPI 통신을 사용한다. 