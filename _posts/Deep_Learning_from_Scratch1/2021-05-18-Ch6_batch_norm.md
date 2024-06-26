---
layout: single
title:  "Deep Learning from Scratch1 - Ch6 배치정규화 알고리즘"
excerpt: "배치정규화(Batch Normalization)"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 배치정규화(Batch Normalization) 알고리즘
- 2015년에 제안된 방법으로 각 층이 활성화를 적당히 퍼뜨리도록 '강제'하는 알고리즘
- 데이터분포를 정규화하는 '배치정규화(Batch Norm)' 계층을 신경망에 삽입한다.

## 배치정규화의 장점
- 학습을 빠르게 진행할 수 있다.
- 초깃값에 크게 의존하지 않는다.
- 오버피팅을 억제한다.

## 수식

<img src="https://user-images.githubusercontent.com/59792046/118521686-39e0f480-b776-11eb-926b-14c958b285b6.jpg" width="700">

- 미니배치 B={x1, x2, ... xm}이라는 m개의 입력 데이터의 집합에 대해 평균과 분산을 구한다.
- 입력 데이터를 평균이 0, 분산이 1이 되도록 정규화한다.
- ε(엡실론)은 작은 값으로, 0으로 나누는 사태를 예방한다.
- 이 단계를 활성화함수 앞(혹은 뒤)에 삽입함으로써 데이터 분포를 적절하게 만들 수 있다.

<img src="https://user-images.githubusercontent.com/59792046/118522556-297d4980-b777-11eb-917d-d5f9b1c40df7.jpg" width="500">

- 배치 정규화 계층마다 정규화된 데이터에 확대(scale)와 이동(shift)변환을 수행한다. 
- γ가 확대를, β가 이동을 담당하고, 처음에는 γ=1, β=0부터 시작하여 학습을 통해 적합한 값으로 조정해간다.

## 배치정규화의 효과
- MNIST 데이터셋을 사용하여 배치정규화의 학습효과를 확인
- 초깃값 분포를 다르게 두고 학습 진행이 어떻게 달라지는지 관찰
- 실선이 배치정규화를 사용한 경우, 점선이 사용하지 않은 경우

<img src="https://user-images.githubusercontent.com/59792046/118523502-26368d80-b778-11eb-8adc-a598c3218359.png" width="700">

- 거의 모든 경우에서 배치정규화를 사용할 때의 학습진도가 빠른 것으로 나타난다.
- 배치정규화를 이용하지 않은 경우에 초깃값이 잘 분포되어 있지 않으면 학습이 전혀 진행되지 않는 모습도 확인할 수 있다.

#### [참고자료]
- 밑바닥부터 시작하는 딥러닝