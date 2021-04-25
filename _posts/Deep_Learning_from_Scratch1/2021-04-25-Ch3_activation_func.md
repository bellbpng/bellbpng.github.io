---
layout: single
title:  "Deep Learning from Scratch1 - Ch3 활성화함수 정리"
---

# 활성화 함수(activation function)
- 입력신호의 총합을 출력신호로 변환하는 함수
- 입력신호의 총합이 활성화를 일으키는지를 정하는 함수
- 단층 퍼셉트론은 단층 네트워크에서 계단 함수(step function)를 사용
- 다층 퍼셉트론은 신경망(여러 층으로 구성, 입력층, 은닉층, 출력층)에 시그모이드 함수(sigmoid function) 같은 활성화 함수를 사용


## 활성화 함수 예시와 구현
### 계단 함수(step function)
- 임계값을 경계로 출력이 바뀌는 함수
- 0을 경계로 출력이 0->1 or 1->0 으로 변한다.
``` python
def step_function(x):
    return np.array(x > 0, dtype=np.int)
```

### 시그모이드 함수(sigmoid function)
- h(x) = 1/(1+exp(-x)
- s자 모양을 그리는 매끄러운 곡선 형태
- 신경망 분야에서 오래전부터 이용한 함수

``` python
def sigmoid(x):
    return 1/(1+np.exp(-x))
```

### ReLU 함수
- h(x) = x (x>0), 0 (else)
- 최근에 주로 이용하는 함수

```python
def relu(x):
    return np.maximum(0, x)
```

### 신경망에서 활성화 함수로 비선형 함수를 이용해야하는 이유
선형 함수(h(x)=cx)는 아무리 층을 깊게 해도 단일층 네트워크로 똑같은 기능을 구현할 수 있다. 

예를들어, 3층 네트워크의 경우 _y(x) = C x C x C x X_ 로 y=ax의 형태와 같아 여러층의 이점을 살릴 수 없다.

#### [참고자료]
- 밑바닥부터 시작하는 딥러닝

