---
layout: single
title:  "시퀀셜 API로 이미지 분류기 만들기"
excerpt: "시퀀셜 API로 이미지 분류기 만들기"

categories:
  - ML Project
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---


본 내용은 "핸즈온 머신러닝 2판"에 수록된 내용으로 개인 공부 및 기록용으로 포스팅함을 밝힙니다.



## 케라스를 사용하여 데이터셋 적재하기

```python
# import packages
import tensorflow as tf
from tensorflow import keras
import numpy as np

# 데이터 가져오기
fashion_mnist = keras.datasets.fashion_mnist
(X_train_full, y_train_full), (X_test, y_test) = fashion_mnist.load_data()

# 데이터 확인
X_train_full.shape # (60000, 28, 28), 60,000개의 흑백 이미지. 각 이미지 크기는 28x28
X_train_full.dtype # 각 픽셀의 강도는 0~255로 표현

# 훈련세트 분할 --> (작은)훈련세트, 검증세트 // 스케일 조정(SGD를 사용할 것이기 때문)
X_valid, X_train = X_train_full[:5000] / 255., X_train_full[5000:] / 255.
y_valid, y_train = y_train_full[:5000] / 255., y_train_full[5000:] / 255.
X_test = X_test / 255.

y_train # 레이블 확인 (0~9로 분류된 클래스 ID)

class_names = ["T-shirt/top", "Trouser", "Pullover", "Dress", "Coat"
                "Sandal", "Shirt", "Sneaker", "Bag", "Ankle boot"]

X_valid.shape # (5000, 28, 28)
X_test.shape # (10000, 28, 28)
```
- 케라스는 MNIST, 패션 MNIST, 캘리포니아 주택 데이터셋을 포함하여 널리 사용되는 데이터셋을 다운로드하고 적재할 수 있는 유틸리티 함수를 제공한다.
- 사이킷런 대신 케라스를 사용하여 MNIST나 패션 MNIST 데이터를 적재할 때 중요한 점은 각 이미지가 784 크기의 1D 배열이 아니라 28x28 크기의 2D 배열이라는 점이다. 픽셀 강도도 실수가 아닌 정수(0~255)까지로 표현되어 있다.


## 모델 만들기

```python
# Sequential API를 이용해서 모델 만들기(input, hidden1, hidden2, output)
model = keras.models.Sequential()
model.add(keras.layers.Flatten(input_shape=[28, 28]))
model.add(keras.layers.Dense(300, activation="relu"))
# activation = keras.activations.relu 로 지정하는 것과 동일
model.add(keras.layers.Dense(100, activation="relu"))
model.add(keras.layers.Dense(10, activation="softmax"))

# 다른 방법 - API 객체 생성시 리스트의 형태로 전달
model = keras.models.Sequential([
    keras.layers.Flatten(input_shape=[28, 28]),
    keras.layers.Dense(300, activation="relu"),
    keras.layers.Dense(100, activation="relu"),
    keras.layers.Dense(10, activation="softmax")
])

# 층의 정보를 확인하는 방법
hidden1 = model.layers[1]
weights, biases = hidden1.get_weights()
weights.shape # (784,300)

# 모델 컴파일
# loss = keras.losses.sparse_categorical_crossentropy 와 동일
# optimizer = keras.optimizers.SGD() 와 동일
# metrics = keras.metrics.sparse_categorical_accuracy 와 동일
model.compile(loss="sparse_categorical_crossentropy", # 레이블이 0~9 정수를 가지는 희소(sparse) 레이블
            optimizer="sgd",
            metrics=["accuracy"])
```

<img src="https://user-images.githubusercontent.com/59792046/168315573-9ca7d8c9-b4ee-4911-91db-fcb80cb3e14b.png" width=700>


- Sequential 모델은 가장 간단한 케라스의 신경망 모델이다. 순서대로 연결된 층을 일렬로 쌓아서 구성한다.
- Flatten 층은 입력 이미지를 1D 배열로 변환한다. 즉, 입력 데이터 X를 받으면 X.reshape(-1,1)를 계산한다. input_shape에는 배치 크기를 제외하고 샘플의 크기만 써야한다. 
- Dense 은닉층 2개를 추가한다. 활성화 함수로 두 은닉층 모두 "ReLU" 함수를 사용한다. Dense 층은 각자 가중치 행렬을 관리하고 이 행렬에는 층의 뉴런과 입력 사이의 모든 연결 가중치가 포함된다. 또 편향도 벡터로 관리한다.
- 인덱스로 각 층에 쉽게 접근할 수 있다.
- compile() 메서드를 호출하여 사용할 손실함수와 옵티마이저(optimizer)를 지정해야 한다. 
- 레이블이 정수 하나로 이루어져 있고, 클래스가 서로 배타적인 경우 "sparse_categorical_crossentropy" 손실을 사용한다.
- 레이블이 원-핫 벡터라면 "categorical_crossentropy" 손실을 사용한다.
- 이진 분류를 수행한다면 출력층에 "softmax" 함수 대신 "sigmoid" 함수를 사용하고 "binary_crossentropy" 손실을 사용한다.


## 모델 훈련과 평가, 예측

```python
# 모델 훈련과 평가
history = model.fit(X_train, y_train, epochs=30,
                    validation_data=(X_valid, y_valid))

model.evaluate(X_test, y_test) # 일반화 오차 확인, loss, accuracy 반환

# 새로운 데이터로 모델을 사용해 예측 만들기
X_new = X_test[:3] # 3개의 샘플 데이터를 훈련 세트에서 가져옴
y_proba = model.predict(X_new) # 3개의 데이터에 대해 클래스에 속할 확률이 예측됨
y_proba.round(2)

y_pred = np.argmax(model.predict(X_new), axis=1) # 확률이 가장 높은 열의 인덱스 출력

np.array(class_names)[y_pred] # y_pred 원소에 해당하는 class_names 으로 배열을 만듦

```
- 모델을 평가하여 일반화 오차를 추정할 때, evaluate() 메서드를 활용한다.



#### [참고자료]
- 핸즈온 머신러닝 2판





