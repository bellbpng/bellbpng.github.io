---
layout: single
title:  "Deep Learning from Scratch1 - Ch6 최적화기법"
excerpt: "최적화기법- SGD, Momentum, AdaGrad, Adam"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 최적화(Optimization)
- 신경망 학습의 목적은 손실 함수의 값을 가능한 한 낮추는 매개변수를 찾는 것이다.
- 이런 매개변수 최적값을 찾는 과정을 최적화라고 한다.

## 확률적 경사 하강법(SGD)
- 손실함수에 대한 매개변수의 기울기를 구해 기울어진 방향으로 매개변수 값을 갱신하는 방법
- 비등방성(anisotropy) 함수에서는 탐색 경로가 비효율적이다.

### 수식

<img src="https://user-images.githubusercontent.com/59792046/118385759-e1fa9e80-b64c-11eb-8e36-ad6d43f3e5b3.jpg" width="500">

### 구현

``` python
class SGD:
    def __init__(self, lr=0.01):
        self.lr = lr

    def update(self, params, grads):
        for key in params.keys():
            params[key] -= self.lr * grads[key]
```

### 비등방성 함수(anisotropy function)
- 방향에 따라 성질이 달라지는 함수로 여기서는 특정한 지점에서 기울기의 성질이 변한다는 것을 의미한다.

#### 등방성 함수 예시

<img src="https://user-images.githubusercontent.com/59792046/118385907-2b97b900-b64e-11eb-88f1-dee49b703e45.png" width="500">

<img src="https://user-images.githubusercontent.com/59792046/118385927-4f5aff00-b64e-11eb-8b06-76b095541219.png" width="500">

- 등방성이란 어느 방향에서 보아도 똑같은 성질을 지니는 성질이다. 
- 함수의 기울기 그래프를 보면 모든 좌표에서 기울기는 항상 원점을 가리키고 있는 것을 알 수 있다.

#### 비등방성 함수 예시

<img src="https://user-images.githubusercontent.com/59792046/118385963-9b0da880-b64e-11eb-8663-92386a8b16b7.png" width="500">

<img src="https://user-images.githubusercontent.com/59792046/118385981-c0021b80-b64e-11eb-9b03-d960af26a548.png" width="500">

- 비등방성 함수에서 기울기 그래프를 살펴보면 각 지점마다 가리키는 방향이 다른 것을 확인할 수 있다.
- SGD는 기울기가 가리키는 방향으로 무조건 이동하게 되므로 탐색경로가 비효율적이고, 학습효과가 상대적으로 떨어진다.

#### 비등방성 함수에 대한 SGD 최적화 갱신 경로

<img src="https://user-images.githubusercontent.com/59792046/118386022-225b1c00-b64f-11eb-8394-b2aacd28eab6.png" width="500">

- 무작정 기울어진 방향으로 진행하다보니 지그재그로 심하게 굽어진 움직임을 보인다.

## 모멘텀(Momentum)
- '운동량'을 뜻하는 단어로 물리와 관계가 깊다.
- 기울기 방향으로 힘을 받아 물체가 가속된다는 물리법칙을 이용한다.

### 수식

<img src="https://user-images.githubusercontent.com/59792046/118386088-bfb65000-b64f-11eb-9bef-9d4471cdd464.jpg" width="700">

### 구현

```python
class Momentum:
    def __init__(self, lr=0.01, momentum=0.9):
        self.lr = lr
        self.momentum = momentum
        self.v = None

    def update(self, params, grads):
        if self.v is None:
            self.v = {}
            for key, val in params.items():
                self.v[key] = np.zeros_like(val)

            for key in params.keys():
                self.v[key] = self.momentum * self.v[key] - lr*grads[key]
                params[key] += self.v[key]
```

- 인스턴스 변수 v는 속도를 나타내며 초기화 때는 아무 값도 담지 않고, update()가 처음 호출될 때 매개변수와 같은 구조의 데이터를 딕셔너리 변수로 저장한다.

### 비등방성 함수에 대한 모멘텀 최적화 경로

<img src="https://user-images.githubusercontent.com/59792046/118386417-5126c180-b652-11eb-86b7-edcc959cde05.jpg" width="500">

- 공이 그릇 바닥을 구르듯이 움직이는 모습이다. SGD에 비해 지그재그 정도도 덜한 것을 확인할 수 있다.
- x축의 힘은 아주 작지만 방향은 변하지 않아서 한 방향으로 일정하게 가속하고, y축의 힘은 크지만 위아래로 번갈아 받아서 상충하여 y축 방향의 속도는 안정적이지 않다.
- 전체적으로 SGD보다 x축 방향으로 빠르게 다가갈 수 있다.

## AdaGrad
- 학습률 감소(learning rate decay): 학습률을 정하는 효과적 기술로 학습을 진행하면서 학습률을 점차 줄여가는 방법
- 처음에는 크게 학습하다가 조금씩 작게 학습하는 원리로 실제 신경망 학습에 자주 사용된다.
- AdaGrad는 각각의 매개변수에 적응적으로(adaptive) 학습률을 조정하면서 학습을 진행한다. 
- 과거의 기울기를 제곱하여 계속 더해가서 학습을 진행할수록 갱신강도가 약해진다. 따라서, 어느 순간 갱신량이 0이 되어 전혀 갱신되지 않는 상황이 발생할 수 있다. 

### 수식

<img src="https://user-images.githubusercontent.com/59792046/118386540-196c4980-b653-11eb-810b-03d6e62acd64.jpg" width="700">

- 변수 h는 기존 기울기 값을 제곱하여 계속 더해간다. 그리고 매개변수를 갱신할 때 제곱근으로 나누어주어 학습률을 조정한다. 
- 매개변수의 원소 중 많이 움직인(크게 갱신된) 원소는 학습률이 낮아진다는 의미로 매개변수의 원소마다 학습률 감소가 다르게 적용된다.

### 구현

```python
class Adagrad:
    def __init__(self, lr=0.01):
        self.lr = lr
        self.h = None

    def update(self, params, gradsd):
        if self.h is None:
            self.h = {}
            for key, val in params.items():
                self.h[key] = np.zeros_like(val)
        
        for key in params.keys():
            self.h[key] += grads[key]*grads[key]
            params[key] -= self.lr * grads[key] / (np.sqrt(self.h[key]) + 1e-7)
```

### 비등방성 함수에 대한 AdaGrad 최적화 경로

<img src="https://user-images.githubusercontent.com/59792046/118386796-e6c35080-b654-11eb-8000-4d8c2a79e803.jpg" width="500">

- y축 방향은 기울기가 커서 처음에는 크게 움직이지만 크게 움직인만큼 학습률이 큰폭으로 작아진다. 따라서 y축 방향으로 갱신 강도가 빠르게 약해지고, 지그재그 움직임이 줄어든다.

## Adam
- 2015년에 제안된 새로운 방법이다. 이론은 복잡하지만 직관적으로는 모멘텀과 AdaGrad를 융합한 듯한 방법이다.
- 하이퍼파라미터의 '편향 보정'이 진행된다.

### 비등방성 함수에 대한  Adam 최적화 경로

<img src="https://user-images.githubusercontent.com/59792046/118386884-939dcd80-b655-11eb-8ff0-f024cc4119c8.jpg" width="500">

- 모멘텀과 비슷한 패턴이지만 학습률을 적응적으로 조정해서 공의 좌우 흔들림이 적다.

## 네 가지 최적화 방법 비교 그래프

<img src="https://user-images.githubusercontent.com/59792046/118386933-e2e3fe00-b655-11eb-9ee4-0eab39d1b57c.png" width="500">

- 모든 문제에 대해서 항상 뛰어난 기법은 아직까지는 없다.
- 각자의 장단점에 잘 맞추어 문제를 풀어나가야 한다.

## MNIST 데이터셋으로 본 갱신방법 비교

<img src="https://user-images.githubusercontent.com/59792046/118387002-8a613080-b656-11eb-92f7-12ba330e6aff.png" width="500">

- 각 층이 100개의 뉴런으로 구성된 5층 신경망에서 ReLU를 활성화 함수로 사용해 측정한 결과이다.
- 하이퍼파라미터인 학습률과 신경망의 구조에 따라 결과는 달라질 수 있다.
- 일반적으로 SGD보다 다른 세 기법이 빠르게 학습하고, 때로는 최종정확도도 더 높게 나타난다.

#### [참고자료]
- 밑바닥부터 시작하는 딥러닝
- [SGD - 비등방성 함수(anisotropy function)](https://beoks.tistory.com/29)