---
layout: single
title:  "Deep Learning from Scratch1 - Ch4 손실함수 정리"
excerpt: "손실함수 구현과 미니배치"

categories:
  - ML
tags:
  - [ML, Python]

toc: true
toc_sticky: true
---

# 손실함수
- 신경망 학습에서 사용하는 지표로 이를 기준으로 최적의 매개변수 값을 탐색한다.
- 손실함수는 '나쁨'을 나타내는 지표로 손실함수의 값을 최소화하는 방향으로 설정해야한다.


## 오차제곱합(sum of squares for error, SSE)
- E = (1/2) x Σ((y-t)^2)
- y는 신경망의 출력, t는 정답레이블


``` python

#오차제곱합 sum of squares for error
def sum_squares_error(y,t):
    return 0.5*np.sum((y-t)**2)

```

## 교차 엔트로피 오차(cross entropy error, CEE)
- E = -Σ(t x ln(y))
- y는 신경망의 출력, t는 정답레이블
- 정답레이블이 원-핫 인코딩인 경우 실질적으로 정답일 때의 추정(t=1일 때의 y)의 자연로그를 계산한다.
- 신경망의 출력(y)이 1에 가까워질수록 손실함수 값은 0에 수렴한다. 반대로 정답(1)에서 멀어질수록 오차는 커진다. (로그함수 특성)

``` python

#교차엔트로피오차 cross_entropy_error(y,t)
def cross_entropy_error(y,t):
    delta = 1e-7 #0.0000001, 아주 작은 값을 더해서 log 함수에 0이 들어가는 것을 방지
    return -np.sum(t*np.log(y+delta))
    
```

- python에서 log()는 밑이 e인 자연로그를 계산한다.
- log10()은 밑이 10인 상용로그, log(num, base)는 밑이 base인 로그를 계산한다.

## 미니배치 학습
- N개의 훈련데이터에 대한 모든 손실함수의 값을 합하고 N으로 나누어 평균손실함수를 구한다.
- 평균을 이용한다면 훈련데이터의 개수에 상관없이 언제든 통일된 지표를 얻을 수 있다.
- 훈련데이터의 수가 너무 많으면 시간이 오래걸리므로 무작위로 100장 정도만 뽑아 그 100장만을 사용하여 학습하는 방법을 '미니배치 학습'이라고 한다.

### 미니배치 학습과 손실함수 재구현

``` python

#MNIST 데이터셋을 읽어오는 코드
import sys, os
sys.path.append(os.pardir)
import numpy as np
from dataset.mnist import load_mnist

(x_train, t_train), (x_test, t_test) = \
    load_mnist(normalize=True, one_hot_label=True)
    
print(x_train.shape) #60000행, 784열의 훈련데이터
print(t_train.shape) #60000행, 10열의 훈련 정답레이블

train_size = x_train.shape[0] #60000개의 데이터
batch_size = 10
batch_mask = np.random.choice(train_size, batch_size) #(60000,10) 0이상 60000미만 수 중 10개를 무작위로 추출
x_batch = x_train[batch_mask]
t_batch = t_train[batch_mask]

#배치용 교차엔트로피 구현
def cross_entropy_error(y,t):
    if y.ndim==1 : # y가 1개의 데이터를 처리히는 경우(행의 개수가 1)
        t = t.reshape(1, t.size) #size는 전체 원소의 개수를 반환한다.
        y = y.reshape(1, y.size)
    batch_size_entropy = y.shape[0] #y의 행의개수(데이터의 개수)
    return -np.sum(t*np.log(y+1e-7))/batch_size_entropy

#정답레이블이 원-핫 인코딩이 아닌 경우 배치용 교차엔트로피
def cross_entropy_error2(y,t):
    if y.ndim == 1:
        t = t.reshape(1, t.size)
        y = y.reshape(1, y.size)
    
    batch_size_entropy = y.shape[0]
    return -np.sum(np.log(y[np.arange(batch_size_entropy),t] + 1e-7)) / batch_size_entropy


"""
np.log(y[np.arange(batch_size_entropy), t])에 대한 설명
- np.arange(batch_size)는 0부터 (batch_size_entropy-1) 까지 1간격으로 배열을 생성한다.
- if batch_size_entropy == 5 then [0, 1, 2, 3, 4]
- t 에는 [2,7,0,9,4]와 같이 레이블이 담겨있으므로, 
- y[np.arange(batch_size_entropy), t]는 각 데이터의 정답 레이블에 해당하는 신경망의 출력을 추출한다.
- 위의 예에서는 y[0,2], y[1,7], y[2,0], y[3,9], y[4,4]인 넘파이 배열이 형성된다.
"""

```

- np.random.choice(a, b): np.arange(a)에서 b개의 샘플을 무직위로 뽑은 1차원 배열을 반환한다.
- if np.random.choice(10, (2,3)), np.arange(10)에서 무작위로 샘플을 뽑은 2x3 형태의 행렬을 반환한다.
- np.arange([start], stop, [step]): start와 step은 생략이 가능하고, 생략되면 각각 0, 1 값을 가지고, stop-1까지 배열을 형성한다.
- x_batch = x_train[batch_mask]: 1차원 배열에서 인덱스 배열을 활용하는 방법이다. 

    => 무작위로 추출된 10개의 원소를 가지는 배열 batch_mask를 인덱스로 이용해서, x_train에서 해당 원소를 뽑아내어 배열을 형성한다.
- t.size 는 넘파이배열 t의 전체 원소의 개수를 반환한다.


## 손실함수를 설정하는 이유
- 신경망 학습에서는 최적의 매개변수(가중치와 편향)를 탐색할 때 손실함수의 값을 가능한 한 작게하는 매개변수 값을 찾는다.
- 이때, 매개변수의 미분값을 계산하고 그 미분값을 단서로 매개변수의 값을 서서히 갱신하는 과정을 반복한다.
- 정확도를 지표로하면 매개변수의 미분이 대부분의 장소에서 0이 되어 가중치 매개변수의 갱신이 멈춘다.
- 따라서, 신경망 학습에서 중요한 성질은 기울기가 0이 되지 않도록 하는 것이다.

### [참고자료]
- 밑바닥부터 시작하는 딥러닝
- Numpy 난수 생성(Random 모듈) https://codetorial.net/numpy/random.html
- (파이썬) numpy.arange - 코딩 연습 https://codepractice.tistory.com/88
- NumPy(넘파이)에서의 indexing  https://kongdols-room.tistory.com/58

