---
layout: single
title:  "Deep Learning from Scratch1 - Ch3 3층 신경망 구현"
excerpt: "코드로 이해하는 3층 신경망"

categories:
  - ML
tags:
  - [ML, Python]

toc: true
toc_sticky: true
---

# 3층 신경망에서 수행되는 순방향 처리(입력부터 출력까지) 구현
- 입력층(0층), 은닉층(1층, 2층), 출력층(3층)
- 신경망에서의 계산을 행렬 계산으로 보다 쉽고 간단하게 정리할 수 있음을 보인다.

## 표기법
<img src = "https://user-images.githubusercontent.com/59792046/114652037-10205200-9d20-11eb-80dc-741f703fe794.jpg" width = "500">


## 활성화 함수를 적용하여 3층 신경망 총 구현
<img src = "https://user-images.githubusercontent.com/59792046/114652474-e74c8c80-9d20-11eb-8c94-233fce9ae6dc.jpg" width = "500">



```python
# 3층 신경망 구현 정리
def init_network():
    network = {}  # 딕셔너리 선언
    network['W1'] = np.array([[0.1, 0.3, 0.5], [0.2, 0.4, 0.6]])
    network['b1'] = np.array([0.1, 0.2, 0.3])
    network['W2'] = np.array([[0.1, 0.4], [0.2, 0.5], [0.3, 0.6]])
    network['b2'] = np.array([0.1, 0.2])
    network['W3'] = np.array([[0.1, 0.3], [0.2, 0.4]])
    network['b3'] = np.array([0.1, 0.2])

    return network

# 신호의 순전파(입력에서 출력방향)를 구현
def forward(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3'] # W1은 2*3, W2는 3*2, W3는 2*2 행렬
    b1, b2, b3 = network['b1'], network['b2'], network['b3'] # 편향은 모두 1차원 배열

    a1 = np.dot(x, W1) + b1 # 1차원 배열
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)
    a3 = np.dot(z2, W3) + b3
    y = identity_function(a3)

    return y


network = init_network()
x = np.array([1.0, 0.5]) # 1차원이고 원소가 2개인 배열
y = forward(network, x)
print(y)

```
- 딕셔너리 변수 network에 가중치(W), 편향(b) 입력
- 은닉층의 활성화함수로 시그모이드 함수(sigmoid function)를 이용하여 중간 출력을 결정
- 출력층의 활성화함수로 항등함수(identity function)을 적용


## 출력층의 활성화함수
- 일반적으로 회귀에는 항등 함수(identity function), 분류에는 소프트맥스 함수(softmax function)를 사용
- 분류(classification)는 데이터가 어느 클래스에 속하는지, 회귀(regression)는 입력 데이터에서 연속적인 수치를 예측하는 문제이다.
- 은닉층의 활성화함수와 구분하기 위해 h(x) 대신 σ(x)(시그마)로 표기한다.


### 항등함수 구현
- 입력과 출력이 항상 같은 함수

``` python
def identity_function(x):
    return x
```

### 소프트맥스 함수 구현
- y = exp(a)/Σ(exp(a))
- 프로그래밍 언어로 구현할 때 발생할 수 있는 오버플로를 방지하기 위해 식을 개선
- y = (C x exp(a))/(C x Σ(exp(a))) = exp(a+lnC)/Σ(exp(a+lnC)) = exp(a+C')/Σ(exp(a+C'))
- exp(x)는 단조증가함수(monotonic function), if a<=b then f(a)<=f(b), 이므로 소프트맥스 함수는 원소의 대소관계에 영향을 주지 않는다.
- 소프트맥스 함수의 출력의 총합은 1이다. 따라서 이 성질을 이용하면 소프트맥스 함수의 출력을 확률로 해석할 수 있다. 따라서, 확률이 가장 높은 원소를 정답으로 판단할 수 있다.
- 신경망을 이용한 분류에서는 가장 큰 출력을 내는 뉴런에 해당하는 클래스로만 인식한다. 
- 추론 단계(결과를 도출하는)에서는 소프트맥스 함수의 생략이 일반적이다.(지수함수에 의한 자원 낭비를 막고자)
- 학습 단계(신경망을 훈련시키는)에서는 소프트맥스 함수를 사용한다.


``` python
def softmax(a):
    c = np.max(a) # 어떤 정수를 더해도 상관없지만, 입력신호 중 최대값을 이용하는 것이 일반적
    exp_a = np.exp(a-c)  # 오버플로 방지, 지수함수에 입력되는 값을 최소로 하기 위해 음수형태의 덧셈을 진행
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a

    return y
```

- np.max() : 배열의 최댓값을 반환
- np.exp() : 자연상수 e의 지수함수를 의미
- np.sum() : 배열의 모든 원소의 합을 반환


#### [참고자료]
- 밑바닥부터 시작하는 딥러닝
