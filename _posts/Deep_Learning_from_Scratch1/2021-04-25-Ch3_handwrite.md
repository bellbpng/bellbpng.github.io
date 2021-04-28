---
layout: single
title:  "Deep Learning from Scratch1 - Ch3 손글씨 숫자 처리"
excerpt: "손글씨 숫자 처리 분석"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 손글씨 숫자 분류
- MNIST 데이터셋에서 손글씨 숫자 이미지를 보고 분류하는 추론 과정 구현
- 훈련 이미지 60,000장 시험 이미지 10,000장
- 28x28 크기의 회색조 이미지이며, 각 픽셀은 0~255 값을 취한다.
- 각 이미지에는 정답 레이블이 붙어 있다.
- 신경망의 순전파(forward propagation) 과정

## 데이터 불러오기
```python
import sys, os
sys.path.append(os.pardir)
from dataset.mnist import load_mnist

# (훈련이미지, 훈련레이블), (시험이미지, 시험레이블) 형식으로 반환
(x_train, t_train), (x_test, t_test) = \
    load_mnist(
        flatten=True, normalize=True, one_hot_label=Flase)  # flatten = True, 읽어들인 이미지는 1차원 넘파이 배열로 저장됨
```

- dataset 폴더가 작업 중인 파일의 부모 디렉터리에 있을 때, 부모 디렉터리의 파일을 가져올 수 있도록 설정
- dataset 하위 파일인 mnist.py에서 load_mnist 함수를 임포트 한다.
- load_mnist 함수의 반환형에 맞추어 데이터를 불러온다.

### MNIST 데이터셋 읽기

``` python
def load_mnist(normalize=True, flatten=True, one_hot_label=False):
 
    if not os.path.exists(save_file):
        init_mnist()
        
    with open(save_file, 'rb') as f:
        dataset = pickle.load(f)
    
    if normalize:
        for key in ('train_img', 'test_img'):
            dataset[key] = dataset[key].astype(np.float32)
            dataset[key] /= 255.0
            
    if one_hot_label:
        dataset['train_label'] = _change_one_hot_label(dataset['train_label'])
        dataset['test_label'] = _change_one_hot_label(dataset['test_label'])    
    
    if not flatten:
         for key in ('train_img', 'test_img'):
            dataset[key] = dataset[key].reshape(-1, 1, 28, 28)

    return (dataset['train_img'], dataset['train_label']), (dataset['test_img'], dataset['test_label']) 

```
    
    Parameters
    ----------
    normalize : 이미지의 픽셀 값을 0.0~1.0 사이의 값으로 정규화할지 정한다.
    one_hot_label : 
        one_hot_label이 True면、레이블을 원-핫(one-hot) 배열로 돌려준다.
        one-hot 배열은 예를 들어 [0,0,1,0,0,0,0,0,0,0]처럼 한 원소만 1인 배열이다.
    flatten : 입력 이미지를 1차원 배열로 만들지를 정한다. 
    
    Returns
    -------
    (훈련 이미지, 훈련 레이블), (시험 이미지, 시험 레이블)
    
- normalize가 false이면 입력 이미지의 픽셀은 원래 값 그대로 0~255 사이의 값을 가진다.
- one_hot_label 이 true 이면 레이블(정답)을 원-핫 인코딩(one-hot encoding) 형태로 저장한다. 정답을 뜻하는 원소만 1이고 나머지는 모두 0인 배열을 의미한다.
- flatten은 입력이미지를 평탄하게, 즉 1차원 배열로 만들지를 결정한다.

## 신경망의 추론 처리
- 추론을 수행하는 신경망 구현
- 입력층 뉴런은 28x28 = 784개, 출력층 뉴련은 0~9 숫자를 구분하므로 10개로 구성한다.
- 은닉층은 총 2개로 첫 번째 은닉층은 50개의 뉴런, 두 번째 은닉층은 100개의 뉴런을 배치한다. (임의로 정한 값)

``` python
import pickle
import numpy as np
import sys, os
sys.path.append(os.pardir)
from dataset.mnist import load_mnist
from common.functions import sigmoid, softmax

def get_data():
    (x_train, t_train), (x_test, t_test) = \
        load_mnist(normalize=True, flatten=True,
                   one_hot_label=False)  # 입력 데이터를 정규화. 데이터 전처리 작업
    return x_test, t_test # x_test는 훈련용, 시험용데이터를, t_test는 각 데이터의 레이블(정답)을 반환한다.
    
def init_network():
    with open("sample_weight.pkl", 'rb') as f:
        network = pickle.load(f)

    return network
    
def predict(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']

    a1 = np.dot(x, W1) + b1
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)
    a3 = np.dot(z2, W3) + b3
    y = softmax(a3)

    return y  # 각 레이블의 확률을 넘파이 배열로 반환
    
x, t = get_data() # x에는 입력데이터, t에는 입력데이터에 대한 레이블이 저장된다.

network = init_network()

accuracy_cnt = 0
for i in range(len(x)):
    y = predict(network, x[i])
    p = np.argmax(y)  # 확률이 가장 높은 원소의 인덱스 반환
    if p == t[i]:
        accuracy_cnt += 1

print("Accuracy:" + str(float(accuracy_cnt / len(x))))

```
- get_data() 함수를 통해 데이터를 불러오고, 딕셔너리 형태로 pickle 파일에 저장된 가중치와 편향 값을 이용하여 신경망을 형성한다.
- 반복문을 이용하여 훈련데이터에 저장된 이미지 데이터를 1장씩 꺼내 추론한다. 
- 각 이미지 데이터에 대해서 확률(소프트맥스 함수의 출력이 가장 큰 값)이 가장 높은 결과를 도출하여 정답레이블(t에 저장된 데이터)과 비교한다.
- np.argmax() : 배열을 이루는 원소들 중 최대값의 위치(indice)를 반환한다. 
- 손글씨 숫자 추론에서는 위치(index)정보가 각 숫자를 의미한다. 따라서 확률이 가장 높은 원소의 index가 정답레이블과 일치하는지 확인한다.
- 마지막으로, (올바른 결과를 도출한 횟수)/(추론에 사용한 전체 훈련데이터 개수) 를 계산해 정확도를 평가한다. 

### pickle 모듈
- 파이썬에서 텍스트 이외의 자료형(파이썬 객체 자체)을 파일로 저장할 때 사용하는 모듈
- 파일에서 미리 필요한 부분을 딕셔너리, 리스트, 튜플 등으로 저장을 해놓고 pickle 모듈을 활용해 객체 자체를 바이너리 데이터로 저장한다.
- pickle.dump("data.pickle", "wb") 의 형태로 저장해둔 파일에 대해서 pickle.load("data.pickle", "rb")로 불러올 수 있다.

## 배치처리를 통한 재구현
- 위에서 구현한 신경망의 추론과정은 반복문을 통해 이미지 1장씩, 모든 입력데이터에 대해 추론한다.
- 입력데이터 1장이 아닌 여러 장을 한꺼번에 묶어서 predict() 함수에 한 번에 넘기도록 x의 형상을 바꿔준다.
- 입력데이터 X의 형상을 1x784 -> 100x784 로 바꾸어주면 출력 데이터 Y의 형상도 100x10이 된다. 따라서, 100장 분량의 입력데이터의 결과를 한번에 출력할 수 있다.

``` python

x, t = get_data()
network = init_network()

batch_size = 100 # 배치크기 설정
accuracy_cnt = 0

for i in range(0, len(x), batch_size):
    x_batch = x[i:i+batch_size]
    y_batch = predict(network, x_batch)
    p = np.argmax(y_batch, axis = 1)
    accuracy_cnt += np.sum(p == t[i:i+batch_size]) # 배치 단위로 분류한 결과를 실제 답과 비교

print("Accuracy: " + str(float(accuracy_cnt)/len(x)))

```

- 100개의 이미지 데이터씩 묶어서 predict() 함수에 넘겨준다. 
- range(start, end, step) 으로 for문을 구성한다.
- x[0], y[0]에는 0번째 이미지와 그 추론 결과가 x[1], y[1]에는 1번째 이미지와 그 추론 결과가 저장된다.
- np.argmax(y_batch, axis=1)에서 axis=1로 설정한 이유는 각 행(이미지 데이터)에 해당하는 원소에서 최대값을 도출해야하기 때문이다. 세로는 0차원, 가로는 1차원으로 이해하면 쉽다.
- np.sum() : 배열의 모든 원소의 합을 반환한다.


### 배치처리의 이점
- 수치 계산 라이브러리 대부분이 큰 배열을 효율적으로 처리할 수 있도록 고도로 최적화되어 있다.
- 데이터를 읽어들이는 I/O는 CPU나 GPU의 계산 시간에 비해 상당히, 매우 느리다. 따라서 데이터를 읽어들이는 횟수가 많아질수록 병목으로 작용하는 경우가 생기는데 배치처리를 통해 버스에 주는 부하를 줄여줄 수 있다.
- 결론적으로 컴퓨터는 큰 배열을 한꺼번에 계산하는 것이 작은 배열을 여러 번 계산하는 것보다 빠르다.

#### [참고자료]
- 밑바닥부터 시작하는 딥러닝
- [[python] 파이썬 pickle 피클 활용법](https://korbillgates.tistory.com/173)


