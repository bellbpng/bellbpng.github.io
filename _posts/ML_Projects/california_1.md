---
layout: single
title:  "캘리포니아 주택가격 예측하기1"
excerpt: "캘리포니아 주택가격 예측 프로젝트"

categories:
  - ML Project
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

"캘리포니아 주택가격 예측하기" 프로젝트는 "핸즈온 머신러닝 2판" 도서에 수록된 내용으로 개인 공부 및 기록을 위한 포스팅임을 밝힙니다.

모든 소스코드는 구글 코랩(Colab)에서 실행되었습니다.

코드와 함께 주석으로 문법, 기능적인 설명을 추가했고 이론적으로 필요한 개념이 있다면 코드 밑에 코멘트를 추가하였습니다.


# 프로젝트 이해하기
- StatLib 저장소에 있는 캘리포니아 주택 가격(California Housing Prices) 데이터셋을 사용한다.
- 데이터에는 캘리포니아의 블록 그룹(block group)마다 인구(population), 중간 소득(median income), 중간 주택 가격(median housing price) 등이 담겨있고, 이 데이터로 모델을 학습시켜서 다른 측정 데이터가 주어졌을 때 구역의 중간 주택 가격을 예측하는 모델을 만든다.

## 문제 정의
- 여러 개의 특성을 가지고 하나의 값을 예측하는 모델. 다중 회귀(multiple regression) & 단변량 회귀(univariable regression)

## 성능 측정 지표 선택
- 회귀 문제의 전형적인 성능 지표는 평균 제곱근 오차(RMSE)

<img src="https://user-images.githubusercontent.com/59792046/168062663-aafa9cd6-42e0-4980-9b99-af150c0950bb.png" width=500>

## 필수 모듈 import

```python
#------import necessary pacakges------

# python >= 3.5 필수
import sys
assert sys.version_info >= (3, 5)

# 사이킷런 >= 0.20 필수
import sklearn
assert sklearn.__version__>="0.20"

# 공통 모듈 
import numpy as np
import os

# 깔끔한 그래프 출력을 위해
import matplotlib as mpl
import matplotlib.pyplot as plt
mpl.rc('axes', labelsize=14)
mpl.rc('xtick', labelsize=12)
mpl.rc('ytick', labelsize=12)

# 그림을 저장할 위치
PROJECT_ROOT_DIR="."
CHAPTER_ID="end_to_end_project"
IMAGES_PATH = os.path.join(PROJECT_ROOT_DIR, "images", CHAPTER_ID) # 현재 디렉토리 위치에서 \images\end_to_end_project 에 저장되는 경로
os.makedirs(IMAGES_PATH, exist_ok=True)
```

## 데이터 가져오기

```python
#------데이터 가져오기------

# 데이터 다운로드 하기
import os
import tarfile # 파일을 압축하거나 압축을 해제할 때 사용하는 라이브러리
import urllib.request # url을 열기 위한 라이브러리

DOWNLOAD_ROOT = "https://raw.githubusercontent.com/rickiepark/handson-ml2/master/"
HOUSING_PATH = os.path.join("datasets", "housing")
HOUSING_URL = DOWNLOAD_ROOT + "datasets/housing/housing.tgz"

def fetch_housing_data(housing_url=HOUSING_URL, housing_path=HOUSING_PATH):
    if not os.path.isdir(housing_path):
        os.makedirs(housing_path)
    tgz_path = os.path.join(housing_path, "housing.tgz") # 내 작업환경에 저장할 경로와 파일이름
    urllib.request.urlretrieve(housing_url, tgz_path) # housing_url에서 읽어온 데이터를 tga_path(내 작업환경)에 저장
    housing_tgz = tarfile.open(tgz_path)
    housing_tgz.extractall(path=housing_path) # datasets/housing 하위 모든 압축파일을 해제함
    housing_tgz.close()

fetch_housing_data()

import pandas as pd

def load_housing_data(housing_path=HOUSING_PATH):
    csv_path = os.path.join(housing_path, "housing.csv")
    return pd.read_csv(csv_path) # 판다스로 csv파일 읽어오기
```

### 데이터 구조 살펴보기

```python
# 데이터 구조 살펴보기
housing = load_housing_data()
housing.head() # 데이터의 상단(default=5) 개수를 보여줌
housing.info() # 데이터의 전반적인 정보. 행과 열의 크기, 컬럼명, 개수, 자료형 등
housing["ocean_proximity"].value_counts() # "ocean_proximity" 특성(열)에 속하는 값들의 개수를 확인
housing.describe() # 데이터의 컬럼별 개수, 평균, 표준편차 등 세부 정보를 확인

import matplotlib.pyplot as plt
housing.hist(bins=50, figsize=(20,15)) # housing 데이터의 히스토그램을 그림. bins는 x축 boundary를 결정
save_fig("attribute_histogram_plots")
plt.show()
```

## 테스트 세트 만들기

```python
#------테스트 세트 만들기------
np.random.seed(42) # 각기 다른 환경에서도 결과가 동일하도록 씨드를 설정

import numpy as np

# OP1. 사이킷런의 train_test_split()과 같은 기능을 하는 함수
def split_train_test(data, test_ratio):
    shuffled_indices = np.random.permuation(len(data)) # np.arange(len(data))의 원소들을 무작위로 섞은 배열을 반환
    test_set_size = int(len(data) * test_ratio)
    test_indices = shuffled_indices[:test_set_size]
    train_indices = shuffled_indices[test_set_size:]
    return data.iloc[train_indices], data.iloc[test_indices] # pandas의 iloc함수는 입력받은 인덱스로 데이터를 선택. 정수값만 받는다.

train_set, test_set = split_train_test(housing, 0.2)
len(train_set)

# OP2. 해시 함수를 활용
from zlib import crc32

def test_set_check(identifier, test_ratio):
    return crc32(np.int64(identifier)) & 0xffffffff < test_ratio * 2**32

def split_train_test_by_id(data, test_ratio, id_column):
    ids = data[id_column]
    in_test_set = ids.apply(lambda id_: test_set_check(id_, test_ratio))
    return data.loc[~in_test_set], data.loc[in_test_set] # iloc함수와 비슷하지만 정수가 아닌 데이터도 입력이 가능하다.

import hashlib

def test_set_check(identifier, test_ratio, hash=hashlib.md5):
    return hash(np.int64(identifier)).digest()[-1] < 256 * test_ratio

housing_with_id = housing.reset_index() # index 열이 추가된 데이터프레임을 반환
train_set, test_set = split_train_test_by_id(housing_with_id, 0.2, "index")
housing_with_id["id"] = housing["longitude"] * 1000 + housing["latitude"]
train_set, test_set = split_train_test_by_id(housing_with_id, 0.2, "id")

# OP3. 사이킷런 모듈 활용
from sklearn.model_selection import train_test_split

train_set, test_set = train_test_split(housing, test_size=0.2, random_state=42)

```
- 훈련 세트와 테스트 세트를 구분해두는 것이 가장 중요하다. 
- 테스트 세트는 모델을 훈련할 때는 절대 모델에게 노출시켜서는 안되는 데이터로 모델의 성능에 대한 검증이 끝난 후 서비스를 론칭하기 전에 최종적으로 성능을 평가할 때만 사용한다.
- 위 과정은 순수한 무작위 샘플링 방식이다. 데이터셋이 충분히 크다면 일반적으로 괜찮지만, 그렇지 않다면 샘플링 편향이 생길 가능성이 크다.



``` python
# 중간소득 그래프를 살펴본다. 연속적인 값이기 때문에 범주화를 해본다.
housing["median_income"].hist() 
housing["income_cat"] = pd.cut(housing["median_income"],
                            bins=[0., 1.5, 3.0, 4.5, 6., np.inf],
                            labels=[1,2,3,4,5])
housing["income_cat"].value_counts()
housing["income_cat"].hist()

# 소득 카테고리(income_cat)를 기반으로 사이킷런을 이용한 계층 범주화
from sklearn.model_selection import StratifiedShuffleSplit

split = StratifiedShuffleSplit(n_split=1, test_size=0.2, random_state=42) 
for train_index, test_index in split.split(housing, housing["incomde_cat"]): # split(나눌 데이터, 범주화 타겟 특성 데이터)
    strat_train_set = housing.loc[train_index]
    strat_test_set = housing.loc[test_index]

# 데이터셋을 골고루 섞었으므로 income_cat 특성은 삭제
for set_ in (strat_train_set, strat_test_set):
    set_.drop("income_cat", axis=1, inplace=True)
```
- 중간 소득이 주택 가격에 영향을 주는 중요한 요소라고 전달받았다. 하지만, 중간 소득이 연속적인 값으로 분포하고 있기 때문에 범주화 작업이 필요하다고 판단했으며  따라서, 소득 카테고리를 만들었다.
- 계층적 샘플링(stratified sampling): 테스트 세트, 훈련 세트가 전체 데이터 세트를 대표할 수 있도록 전체 데이터를 동질의 계층으로 나누고 각 계층에서 올바른 수의 샘플을 추출하는 작업이다.

## 데이터 이해를 위한 탐색과 시각화

```python
# ------ 데이터 이해를 위한 탐색과 시각화 ------
housing = strat_train_set.copy()

# 지리적 데이터 시각화
housing.plot(kind="scatter", x="longitude", y="latitude")
save_fig("bad_visualization_plot")
```

<img src="https://user-images.githubusercontent.com/59792046/168066374-61334e20-8744-48d4-aaa2-8c3c6d3acfeb.png" width=700>

``` python
# 밀집도가 높은 곳을 쉽게 판단할 수 있다
housing.plot(kind="scatter", x="longitude", y="latitude", alpha=0.1) 
save_fig("better_visualization_plot")
```

<img src="https://user-images.githubusercontent.com/59792046/168066475-24538fd6-8250-40b8-915c-7618d48b4ff6.png" width=700>

``` python
# 밀집도를 color bar를 통해 확인할 수 있다
housing.plot(kind="scatter", x="longitude", y="latitude", alpha=0.4,
            s=housing["population"]/100, label="population", figsize=(10,7),
            c="median_house_value", cmap=plt.get_cmap("jet"), colorbal=True,
            sharex=False)
plt.legend()
save_fig("housing_prices_scatterplot")
```

<img src="https://user-images.githubusercontent.com/59792046/168066583-2b00bda2-877d-4743-9b83-be3db3cf5877.png" width=700>

``` python
#Download the California image
images_path = os.path.join(PROJECT_ROOT_DIR, "images", "end_to_end_project")
os.makedirs(images_path, exist_ok=True) # 폴더 생성 경로에 폴더가 없을 경우 자동으로 생성해주는 옵션(exist_ok=True)
DOWNLOAD_ROOT = "https://raw.githubusercontent.com/ageron/handson-ml2/master/"
filename = "california.png"
url = DOWNLOAD_ROOT + "images/end_to_end_project/" + filename
urllib.request.urlretrieve(url, os.path.join(images_path, filename))

# 캘리포니아 지도 위에 데이터 시각화
import matplotlib.image as mpimg
california_img = mpimg.imread(os.path.join(images_path, filename))
ax = housing.plot(kind="scatter", x="longitude", y="latitude", figsize=(10,7),
                s=housing["population"]/100, label="Population",
                c="median_house_value", cmap=plt.get_cmap("jet",
                colorbar=False, alpha=0.4))
plt.imshow(california_img, extent=[-124.55, -113.90, 32.45, 42.05], alpha=0.5,
            cmap=plt.get_cmap("jet"))
plt.ylabel("Latitude", fontsize=4)
plt.xlabel("Longitude", fontsize=4)

prices = housing["Median_house_value"]
tick_values = np.linspace(prices.min(), prices.max(), 11)
cbar = plt.colorbar(ticks=tick_values/prices.max())
cbar.ax.set_yticklabels(["$%dk"%(round(v/1000)) for v in tick_values], fontsize=14)
cbar.set_label("Median House Value", fontsize=16)

plt.legend(fontsize=16)
save_fig("california_housing_prices_plot")
plt.imshow()
```

<img src="https://user-images.githubusercontent.com/59792046/168066713-4ec872cb-02ab-41e1-86c0-9e2c22f5af89.png" width=700>

``` python
# 상관관계 조사
corr_matrix = housing.corr()
corr_matrix["median_house_value"].sort_values(ascending=False)

from pandas.plotting import scatter_matrix

attributes = ["median_house_value", "median_income", "total_rooms", "housing_median_age"]
scatter_matrix(housing[attributes], figsize=(12,8))
save_fig("scatter_matrix_plot")
```

- 중간 주택 가격(target data)과 다른 특성 사이의 상관관계를 측정한다.
- 상관계수는 선형적인 상관관계만 측정하기 때문에 비선형적인 관계는 잡을 수 없다.

<img src="https://user-images.githubusercontent.com/59792046/168067127-e91a0629-1e45-42ce-b4c0-9ab571dbfaa1.png" width=1000>


``` python
# 특성 조합해보기
housing["rooms_per_household"] = housing["total_rooms"]/housing["households"]
housing["bedrooms_per_room"] = housing["total_bedrooms"]/housing["total_rooms"]
housing["population_per_household"] = housing["population"]/housing["households"]

corr_matrix = housing.corr()
corr_matrix["median_house_value"].sort_values(ascending=False)
```
- 여러 특성을 조합해보면서 특성을 추가해보는 것도 머신러닝 알고리즘 데이터를 준비하기 전에 할 수 있는 좋은 아이디어이다.
- 가구 당 방 개수, 방 개수 당 침실 수, 가구 당 인구수 특성을 새롭게 추가했다.
- 마찬가지로 상관계수를 구해서 주택 가격에 얼마나 영향을 미치는지 확인할 수 있다.


#### [참고자료]
- 핸즈온 머신러닝 2판