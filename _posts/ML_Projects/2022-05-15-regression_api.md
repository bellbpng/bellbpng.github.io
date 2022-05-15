---
layout: single
title:  "다양한 API를 사용하여 회귀용 다층 퍼셉트론 만들기"
excerpt: "시퀀셜 API, 함수형 API"

categories:
  - ML Project
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---


본 내용은 "핸즈온 머신러닝 2판"에 수록된 내용으로 개인 공부 및 기록용으로 포스팅함을 밝힙니다.
모든 코드는 코랩(Colab) 환경에서 실행합니다.


## 패키지 임포트

```python
# import necessary packages
import sys
assert sys.version_info >= (3,5)

import sklearn
assert sklearn.__version__ >= "0.20"

import tensorflow as tf
assert tr.__version__>="2.0"
from tensorflow import keras

import numpy as np
import os
import matplotlib as mpl
import matplotlib.pyplot as plt

from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
```


## 시퀀셜 API를 사용하기

```python
# ------시퀀셜 API를 사용한 회귀 MLP ------
# 데이터 가져오기
housing = fetch_california_housing()

X_train_full, X_test, y_train_full, y_test = train_test_split(housing.data, housing.target, random_state=42)

# default test_size is set to 0.25
X_train, X_valid, y_train, y_valid = train_test_split(X_train_full, y_train_full, random_state=42) 

# 특성 스케일링
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_valid = scaler.transform(X_valid)
X_test = scaler.transform(X_test)

# 다른 작업환경에서도 결과를 동일하게 유지
np.random.seed(42)
tf.random.set_seed(42)

# 모델 생성, 훈련, 예측
# 은닉층 1개인 시퀀셜 모델
model = keras.models.Sequential([
    keras.layers.Dense(30, activation="relu", input_shape=X_train.shape[1:]),
    keras.layers.Dense(1)
])

# 모델 컴파일 - 손실함수, 최적화기법 지정
model.compile(loss="mean_squared_error", optimizer=keras.optimizers.SGD(learning_rate=1e-3))

history = model.fit(X_train, y_train, epochs=20, validation_data=(X_valid, y_valid))
mse_test = model.evaluate(X_test, y_test)

# 새로운 데이터(가정) - X_test의 처음 3개의 데이터
X_new = X_test[:3]
y_pred = model.predict(X_new)

```
- 사이킷런의 fetch_california_housing() 함수를 사용하여 캘리포니아 주택 가격 데이터를 적재하였고, 수치 특성만 가지고 누락된 데이터도 없다.
- 이 데이터셋에는 잡음이 많기 때문에 과대적합을 막는 용도로 뉴런 수가 적은 은닉층 하나만을 사용했다.
- 분류에서 사용했던 원리와 매우 비슷하다. 주된 차이점은 출력층이 활성화 함수가 없는 하나의 뉴런으로 이루어져 하나의 값만을 예측한다는 점이다.


## 함수형 API를 사용하기(1)

```python
# ------ 함수형 API 사용 ------
np.random.seed(42)
tf.random.set_seed(42)

# 모델 생성1 - 와이드, 딥 경로 생성
input_ = keras.layers.input(shape=X_train.shape[1:])
hidden1 = keras.layers.Dense(30, activation="relu")(input_)
hidden2 = keras.layers.Dense(30, activation="relu")(hidden1)
concat = keras.layers.concatenate([input_, hidden2])
output = keras.layers.Dense(1)(concat)
model = keras.models.Model(inputs=[input_], outputs=[output])

# 모델 정보 출력
model.summary()

# 모델 컴파일 - 손실함수, 최적화기법 지정
model.compile(loss="mean_squared_error", optimizer=keras.optimizers.SGD(learning_rate=1e-3))

history = model.fit(X_train, y_train, epochs=20, validation_data=(X_valid, y_valid))
mse_test = model.evaluate(X_test, y_test)
y_pred = model.predict(X_new)

```

<img src="https://user-images.githubusercontent.com/59792046/168466427-6dd3d0ab-055a-4e5a-bd6e-b7c9fb342919.png" width=700>

- 순차적이지 않은 신경망의 한 예로 와이드 & 딥(Wide & Deep) 신경망이 있다. 위 구조에서 확인할 수 있듯이 신경망이 복잡한 패턴(은닉층을 활용한)과 간단한 규칙(짧은 경로를 사용한)을 모두 학습할 수 있다. 
- 층 전체에 모든 데이터를 통과시키는 MLP 네트워크는 데이터에 있는 간단한 패턴이 연속된 변환으로 왜곡될 수 있다.
- Input 객체가 필요하며 shape, dtype을 포함하여 모델의 입력을 정의해아 한다. 한 모델은 여러 개의 입력을 가질 수 있다.
- Dense층은 만들어지자마자 입력과 함께 함수처럼 호출된다. 케라스에 층이 연결될 방법을 알려주는 과정이다.
- Concatenate 층을 만들고 함수처럼 호출하여 두 번째 은닉층의 출력과 입력을 연결한다.
- 출력층에 Concatenate 층의 결과를 전달한다.
- 사용할 입력층과 출력층을 지정하여 케라스 Model을 만든다.

## 힘수형 API 사용하기(2)

```python
# 모델 생성2 - 와이드, 딥 경로에 다른 특성을 전달
input_A = keras.layers.Input(shape=[5], name="wide_input") # 5개의 특성을 와이드 경로로 보냄
input_B = keras.layers.Input(shape=[6], name="deep_input") # 6개의 특성을 딥 경로로 보냄
hidden1 = keras.layers.Dense(30, activation="relu")(input_B)
hidden2 = keras.layers.Dense(30, activation="relu")(hidden1)
concat = keras.layers.concatenate([input_A, hidden2])
ouput = keras.layers.Dense(1)(concat)
model = keras.models.Model(inputs=[input_A, input_B], outputs=[output])

# 모델 컴파일 - 손실함수, 최적화기법 지정
model.compile(loss="mse", optimizer=keras.optimizers.SGD(learning_rate=1e-3))

# 데이터 분할, (0~4) 특성은 와이드 경로, (2~7) 특성은 딥 경로
X_train_A, X_train_B = X_train[:, :5], X_train[:, 2:]
X_valid_A, X_valid_B = X_valid[:, :5], X_valid[:, 2:]
X_test_A, X_test_B = X_test[:, :5], X_test[:, 2:]
X_new_A, X_new_B = X_test_A[:3], X_test_B[:3]

# 입력마다 하나씩 행렬의 튜플을 전달
history = model.fit((X_train_A, X_train_B), y_train, epochs=20, validation_data=((X_valid_A, X_valid_B), y_valid))
mse_test = model.evaluate((X_test_A, X_test_B), y_test)
y_pred = model.predict((X_new_A, X_new_B))

```

<img src="https://user-images.githubusercontent.com/59792046/168466733-2119f030-ca0e-4935-80a5-07138d57d611.png" width=700>

- 일부 특성은 짧은 경로로 전달하고 다른 특성들은(중복 가능) 깊은 경로로 전달하는 방법이다. 


## 함수형 API 사용하기(3) - 여러 개의 출력이 필요한 경우

- 여러 개의 출력이 필요한 작업의 경우 사용할 수 있다. 예를 들어, 그림에 있는 주요 물체를 분류하고 위치를 알아야 할 때 회귀작업(물체 중심의 좌표와 너비, 높이)과 분류 작업을 함께하는 경우가 있다.
- 동일한 데이터에서 독립적인 여러 작업을 수행할 때 사용할 수 있다. 작업마다 새로운 신경망을 훈련할 수 있지만, 작업마다 하나의 출력을 가진 단일 신경망을 훈련하는 것이 보통 더 나은 결과를 낸다. 신경망이 여러 작업에 걸쳐 유용한 특성을 학습할 수 있기 때문이다. 

```python
# 모델 생성3 -  규제를 위한 보조 출력 추가하기
input_A = keras.layers.Input(shape=[5], name="wide_input")
input_B = keras.layers.Input(shape=[6], name="deep_input")
hidden1 = keras.layers.Dense(30, activation="relu")(input_B)
hidden2 = keras.layers.Dense(30, activation="relu")(hidden1)
concat = keras.layers.condatenate([input_A, hidden2])
output = keras.layers.Dense(1, name="main_output")(concat)
# 보조 출력층 형성
aux_output = keras.layers.Dense(1, name="aux_output")(hidden2)
model = keras.models.Model(inputs=[input_A, input_B], outputs=[output, aux_output])

# 모델 컴파일 - 손실함수, 최적화기법, 주 출력과 보조 출력의 가중치 부여
model.compile(loss=["mse, mse"], loss_weights=[0.9, 0.1], optimizer=keras.optimizers.SGD(learning_rate=1e-3))

history = model.fit([X_train_A, X_train_B], [y_train, y_train], epochs=20,
                    validation_data=([X_valid_A, X_valid_B], [y_valid, y_valid]))
total_loss, main_loss, aux_loss = model.evaluate(
    ([X_test_A, X_test_B], [y_test, y_test])
)
y_pred_main, y_pred_aux = model.predict([X_new_A, X_new_B])

```

<img src="https://user-images.githubusercontent.com/59792046/168466772-04347770-fbb5-4faa-9db0-cbe0ad9f7e70.png" width=700>


- 위는 규제 기법으로 사용한 예시이다. 보조 출력을 사용해 하위 네트워크가 나머지 네트워크에 의존하지 않고 그 자체로 유용한 것을 학습하는지 확인하며 과대적합을 감소하고 모델의 일반화 성능을 높이도록 제약을 가할 수 있다.
- 각 출력은 자신만의 손실 함수가 필요하다. 따라서 모델 컴파일시 손실의 리스트를 전달해야 한다. 
- 케라스는 기본적으로 나열된 손실을 모두 더하여 최종 손실을 구해 훈련에 사용하기 때문에 손실에 대한 가중치를 지정해주어야 한다. 여기서는 보조 출력은 규제로만 사용되므로 주 출력에 더 많은 가중치를 주었다.
- 출력에 대한 레이블도 지정해주는데 이번 경우에는 같은 것을 예측해야 하므로 동일한 레이블을 사용한다. 




#### [참고자료]
- 핸즈온 머신러닝 2판

