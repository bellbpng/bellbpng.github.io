---
layout: single
title:  "Deep Learning from Scratch1 - Ch5 활성화함수 계층"
excerpt: "활성화함수 계층 구현"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## ReLU 계층
### 계산 그래프

<img src="https://user-images.githubusercontent.com/59792046/118370644-96f87100-b5e3-11eb-9077-b14bd91f4187.jpg" width="500">

- 순전파 때의 입력이 0보다 크면 역전파는 상류의 값을 그대로 하류로 흘려보낸다. 
- 순전파 때의 입력이 0 이하면 역전파 때는 신호를 차단한다.

### 구현

```python
class ReLu:
    def __init__(self):
        self.mask = None
    
    def forward(self, x):
        self.mask = (x<=0)
        out = x.copy()
        out[self.mask] = 0       
        return out

    def backward(self, dout):
        dout[self.mask] = 0
        dx = dout
        return dx
```

- self.mask = (x<=0): 하나의 기법으로 넘파이 배열 x의 원소 중 0 이하인 원소의 인덱스에는 True, 그외는 False로 구성된 넘파이 배열을 형성한다.
- x.copy(): 전혀 다른 메모리공간에 값만 같은 배열을 복사한다. out과 x는 완전히 독립적이다.
- out[self.mask] = 0: mask의 원소 중 True에 해당하는 인덱스의 원소를 0으로 초기화한다.
- dout[self.mask] = 0: 순전파때 입력이 0이하인 원소들에 대해서 역전파의 값은 0이 되어야한다. 바로 위 내용과 같은 원리이다.
- dx=dout: 순전파때 입력이 0보다 큰 경우 상류의 값을 그대로 흘려보낸다.

## Sigmoid 계층
### 계산 그래프

<img src="https://user-images.githubusercontent.com/59792046/118371091-ea6bbe80-b5e5-11eb-8cd7-047cbcd001e0.jpg" width="1000">

- 순전파의 출력 y만으로 역전파를 계산할 수 있다.

### 구현

```python
class Sigmoid:
    def __init__(self):
        self.out = None
    
    def forward(self, x):
        out = 1 / (1 + np.exp(-x))
        self.out = out
        return out

    def backward(self, dout):
        dx = dout * self.out * (1.0 - self.out)
        return dx        
```

- 인스턴스변수 out은 순전파의 출력값을 기억해두기 위함이다.

## Affine 계층
- 신경망의 순전파때 수행하는 행렬의 곱을 기하학에서는 어파인 변환(Affine Transformation)이라고 한다.

### 계산 그래프

<img src="https://user-images.githubusercontent.com/59792046/118371323-13d91a00-b5e7-11eb-96e0-c3c09a2a6e5e.jpg" width="1000">

- dot노드의 역전파는 행렬의 대응하는 차원의 원소 수가 일치하도록 곱을 조립하여 구할 수 있다.
- 순전파의 편향 덧셈은 각각의 데이터에 더해진다. 따라서 역전파 때는 각 데이터의 역전파 값이 편향의 원소에 모여야한다. 
- 편향(b)의 역전파는 각 데이터에 대한 미분을 데이터마다(열의 방향으로) 더해서 구한다. 

### 구현

```python
class Affine:
    def __init__(self, W, b):
        self.W = W
        self.b = b
        self.x = None
        self.dW = None
        self.db = None

    def forward(self, x):
        self.x = x
        out = np.dot(x, self.W) + self.b

        return out

    def backward(self, dout):
        dx = np.dot(dout, self.W.T)
        self.dW = np.dot(self.x.T, dout)
        self.db = np.sum(dout, axis=0)
        
        return dx
```

## Softmax-with-Loss 계층
### 계산그래프

<img src="https://user-images.githubusercontent.com/59792046/118371914-bd210f80-b5e9-11eb-9cff-c09278aa57ba.jpg" width="700">

- 소프트맥스 함수는 입력 값을 정규화하여 출력한다. (출력의 합이 1이 되도록 변형된다.)
- 역전파의 결과를 살펴보면 (Softmax 출력 - 정답 레이블) 이라는 오차를 나타낸다는 것을 알 수 있다.
- 신경망의 역전파에서는 오차가 앞 계층에 전해진다는 의미로 신경망 학습에서 중요한 성질이다.
- 소프트맥스의 손실함수로 "교차엔트로피 오차 함수"를 이용하였기 때문에 이러한 말끔한 결과가 나올 수 있는 것이다.
- 항등함수의 손실함수로 "오차제곱합"을 이용하는 것도 같은 이유때문이다. 

```python
class SoftmaxWithLoss:
    def __init__(self):
        self.loss = None
        self.y = None
        self.t = None

    def forward(self, x, t):
        self.t = t
        self.y = softmax(x)
        self.loss = cross_entropy_error(self.y, self.t)

        return self.loss

    def backward(self, dout=1):
        batch_size = self.t.shape[0]
        dx = (self.y - self.t) / batch_size

        return dx
```

- 인스턴스 변수에 손실함수의 결과, 소프트맥스 계층의 출력, 정답레이블을 담는다.
- 역전파 때는 전파하는 값을 배치의 수(batch_size)로 나눠서 데이터 1개당 오차를 앞 계층에 전파한다.


#### [참고자료]
- 밑바닥부터 시작하는 딥러닝