---
layout: single
title:  "Deep Learning from Scratch1 - Ch4 경사하강법"
excerpt: "수치미분을 활용한 경사하강법"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 경사법(경사하강법, gradient descent method)
- 기울기를 이용해 함수의 최솟값(또는 가능한 한 작은 값)을 찾는 방법
- 기울기가 가리키는 곳이 정말 함수의 최솟값인지 보장할 수 없다.(안장점, 극솟값..)
- 하지만, 그 방향으로 나아가야 함수의 값을 줄일 수 있으므로 최솟값이 되는 장소를 찾는 문제에서는 기울기 정보를 단서로 이용한다.
- 현 위치에서 기울어진 방향으로 일정 거리만큼 이동하고, 다시 기울기를 구해서 또 일정 거리만큼 나아가는 방법이 '경사법'이다. 

## 수식표현
<img src = "https://user-images.githubusercontent.com/59792046/115647528-e63ddf80-a35e-11eb-9d18-d72b85b7adbb.jpg" width = "400">


- η(eta, 에타)는 갱신하는 양을 나타내며, 신경망 학습에서는 학습률(learning rate)이라고 한다.

### 경사법으로 f(x0, x1)=(x0)^2+(x1)^2 의 최솟값 구하기

``` python

#수치미분 구현 - 각 원소 하나하나에 대한 기울기
def _numerical_gradient_no_batch(f,x):
    h = 1e-4 #0.0001
    grad = np.zeros_like(x) #x와 형상이 같고 그 원소가 모두 0인 배열을 만듦
    
    # x의 각 원소에 대해서 수치미분을 구한다.
    for idx in range(x.size):
        tmp_val = x[idx]
        x[idx] = tmp_val + h
        fxh1 = f(x)
        
        x[idx] = tmp_val - h
        fxh2 = f(x)
        
        grad[idx] = (fxh1 - fxh2) / (2*h)
        x[idx] = tmp_val
        
    return grad

# 배치처리된 데이터에 적용할 수 있는 수치미분 함수
def numerical_gradient(f, X):
    if X.ndim == 1:
        return _numerical_gradient_no_batch(f, X)
    else:
        grad = np.zeros_like(X)
        
        for idx, x in enumerate(X):
            grad[idx] = _numerical_gradient_no_batch(f, x)
        
        return grad
    
#간단한 경사하강법 구현
def gradient_descent(f, init_x, lr=0.01, step_num=100):
    x = init_x
    
    for i in range(step_num):
        grad = numerical_gradient(f,x)
        x -= lr*grad
    return x
    
def function_2(x):
    return x[0]**2 + x[1]**2
    
```
- enumerate(): 순서가 있는 자료형(list, tuple, dictionary, string 등)을 입력받아 인덱스와 값을 포함하는 객체를 반환한다.
- for idx, value in enumerate(X) 와 같이 반복문과 주로 사용되며, idx, value는 괄호가 없는 튜플이다.

## 경사법을 사용한 위 내용의 갱신과정
<img src = "https://user-images.githubusercontent.com/59792046/115649123-b04e2a80-a361-11eb-84db-a9936f6815d8.png" width = "500">

- 값이 가장 낮은 장소인 원점에 점차 가까워지는 모습이다.

### 하이퍼파라미터(hyper parameter)
- 훈련 데이터와 학습 알고리즘에 의해 자동으로 획득되는 신경망의 가중치와 편향 같은 매개변수와 달리, 사람이 직접 설정해야하는 매개변수
- 학습률이 대표적인 하이퍼파라미터이다.
- 하이퍼파라미터들은 여러 후보 값 중에서 시험을 통해 가장 잘 학습하는 값을 찾는 과정을 거쳐야 한다.

### [참고자료]
- 밑바닥부터 시작하는 딥러닝
- [range와 enumerate 함수](https://wikidocs.net/20792)
- 혼자 공부하는 파이썬

