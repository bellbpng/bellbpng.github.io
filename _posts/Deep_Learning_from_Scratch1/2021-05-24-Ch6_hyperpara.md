---
layout: single
title:  "Deep Learning from Scratch1 - Ch6 하이퍼파라미터 찾기"
excerpt: "하이퍼파라미터 최적값 찾기"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

# 적절한 하이퍼파라미터 값 찾기
- 각 층의 뉴런 수, 배치 크기, 매개변수 갱신 시의 학습률과 가중치 감소 등 신경망에는 다수의 하이퍼파라미터가 존재한다.
- 적절한 하이퍼파라미터 값을 설정하지 않으면 모델의 성능이 크게 떨어지기도 한다.

## 검증 데이터(validation data)
- 훈련 데이터로는 학습을 하고, 시험 데이터로는 범용 성능을 평가한다.
- 하이퍼파라미터의 성능을 평가할 때는 시험 데이터를 사용해서는 안된다. 시험 데이터를 사용하여 하이퍼파라미터를 조정하게 되면 하이퍼파라미터 값이 시험 데이터에 오버피팅이 되기 때문이다.
- 하이퍼파라미터의 조정용 데이터를 일반적으로 '검증 데이터'라고 부르며, 훈련 데이터 중 일부를 분리하여 사용한다.

### 구현(훈련 데이터의 20%정도를 검증 데이터로 분리)

```python
def shuffle_dataset(x, t):
    """데이터셋을 뒤섞는다.

    Parameters
    ----------
    x : 훈련 데이터
    t : 정답 레이블
    
    Returns
    -------
    x, t : 뒤섞은 훈련 데이터와 정답 레이블
    """
    permutation = np.random.permutation(x.shape[0])
    x = x[permutation,:] if x.ndim == 2 else x[permutation,:,:,:]
    t = t[permutation]

    return x, t
(x_train, t_train), (x_test, t_test) = load_mnist()

# 훈련 데이터 뒤섞기
x_train, t_train = shuffle_dataset(x_train, t_train)

# 20%를 검증 데이터로 분할
validation_rate = 0.20
validation_num = int(x_train.shape[0] * validation_rate)

x_val = x_train[:validation_num]
t_val = t_train[:validation_num]
x_train = x_train[validation_num:]
t_train = t_train[validation_num:]
```

- np.random.permutation(): 숫자가 들어가면 해당 숫자까지의 숫자를 무작위순서로 가지는 배열을 만들고, 배열이 들어가면 해당 배열의 모양은 유지하지만 무작위로 섞은 배열을 만든다.

## 하이퍼파라미터 최적화
- 하이퍼파라미터의 최적값이 존재하는 범위를 조금씩 줄여간다.
- 우선 대략적인 범위를 설정하고 그 범위에서 무작위로 하이퍼파라미터 값을 골라낸(샘플링) 후, 그 값으로 정확도를 평가한다.
- 대략적인 범위는 0.001에서 1.000 사이 같이 10의 거듭제곱 단위인 로그 스케일(log scale)로 지정한다.
- 정확도를 살피면서 여러 번 반복하여 하이퍼파라미터 최적값의 범위를 좁혀간다.
- 그리드 서치(grid search)같은 규칙적인 탐색보다는 무작위로 샘플링해 탐색하는 편이 좋다.
- 딥러닝 학습에는 오랜 시간이 걸리므로 나쁠듯한 값은 일찍 포기하고, 학습을 위한 에폭을 작게 하여 1회 평가에 걸리는 시간을 단축하는 것이 효과적이다.


### 구현
- 하이퍼파라미터의 값을 로그 스케일 범위에서 무작위로 추출한다.
- 가중치 감소 계수를 10의 -8~-4승, 학습률을 10의 -6~-2승 범위부터 시작했다.
- 무작위로 추출한 값을 사용하여 학습을 수행하고, 하이퍼파라미터 값을 다양하게 시도하여 최적값을 찾는다.

```python
weight_decay = 10**np.random.uniform(-8, -4)
lr = 10**np.random.uniform(-6, -2)
```

<img src="https://user-images.githubusercontent.com/59792046/119300382-a179d100-bc9b-11eb-9595-c2e522a7f631.png" width="700">

<img src="https://user-images.githubusercontent.com/59792046/119300386-a2aafe00-bc9b-11eb-94c7-613f34ab898d.PNG" width="700">

- 실선은 검증 데이터에 대한 정확도, 점선은 훈련 데이터에 대한 정확도이다.
- 검증 데이터의 학습 추이를 정확도가 높은 순서로 나열한 것으로, Best-5 정도까지의 결과를 살펴보았다.
- 학습이 잘 진행될 때의 학습률은 0.001 ~ 0.01, 가중치 감소 계수는 10의 -8~-6승 정도라는 것을 알 수 있다.

#### [참고자료]
- 밑바닥부터 시작하는 딥러닝

