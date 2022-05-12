---
layout: single
title:  "캘리포니아 주택가격 예측하기2"
excerpt: "캘리포니아 주택가격 예측 프로젝트"

categories:
  - ML Project
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 머신러닝 알고리즘을 위한 데이터 준비

```python
housing = strat_train_set.drop("median_house_value", axis=1) # 주택가격은 target data로 해당 열을 모두 제거
housing_labels = strat_train_set["median_house_value"].copy()
```

### 데이터 정제

```python
# 데이터 정제
# 비어있는 데이터를 어떻게 처리할 것인지? (상단에서 5개 정도만 예시로 활용)

# Op1. 비어있는 데이터가 있는 샘플은 삭제하는 방법
sample_incomplete_rows = housing[housing.isnull().any(axis=1)].head()
sample_incomplete_rows.dropna(subset=["total_bedrooms"]) 

# Op2. total_bedrooms 특성을 아예 삭제하는 방법
sample_incomplete_rows.drop("total_bedrooms", axis=1)

# Op3. 특정값으로 빈 공간을 채워두는 방법(ex. 중간값)
median = housing["total_bedrooms"].median()
sample_incomplete_rows["total_bedrooms"].fillna(median, inplace=True)

# 사이킷런을 활용해서 특정값으로 빈 공간을 채우는 방법
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy="median")
housing_num = housing.drop("ocean_proximity", axis=1) # 중간값은 수치형 데이터만 다루기 때문에 텍스트 특성 삭제
imputer.fit(housing_num) # housing_num 각 특성의 중간값을 계산해서 배열로 반환
imputer.statistics_ # imputer의 결과를 확인

# 훈련세트를 변환하는 과정
X = imputer.transform(housing_num) # 변형된 특성을 housing_num에 채운다.
housing_tr = pd.DataFrame(X, columns=housing_num.columns, index=housing_num.index)
```
- 데이터를 살펴보면 비어있는 값을 발견할 수 있다. 데이터의 누락은 언제든지 발생할 수 있으므로 이에 대한 대책을 마련해두는 것이 중요하다.
- 크게 세 가지 방법을 제안했고, 사이킷런의 SimpleImputer는 누락된 값을 손쉽게 다루도록 도와준다. 


```python
# 텍스트와 범주형 특성 다루기
housing_cat = housing[["ocean_proximity"]] # 사이킷런은 2차원 배열 형태(Dataframe)로 데이터를 다루도록 되어있다.

# Op1. OrdinalEncoder 로 카테고리를 숫자로 표현
from sklearn.preprocessing import OrdinalEncoder

ordinal_encoder = OrdinalEncoder()
housing_cat_encoded = ordinal_encoder.fit_transform(housing_cat)
ordinal_encoder.categories_ # 카테고리가 어떤 순서로 숫자와 되었는지 확인

# Op2. One-hot-encoder 사용, 희소행렬 반환
from sklearn.preprocessing import OneHotEncoder

cat_encoder = OneHotEncoder()
housing_cat_1hot = cat_encoder.fit_transform(housing_cat)
```
- 사이킷런은 pandas의 dataframe 형태의 데이터를 처리하므로 이에 맞춰주어야 한다.
- OrdinalEncoder는 범주형 특성에 순서대로 번호를 매긴다.
- 위 표현 방식은 머신러닝 알고리즘이 가까이 있는 두 값이 떨어져 있는 두 값보다 더 비슷하다고 생각하는 문제가 발생한다. 
- 이러한 문제를 해결하기 위한 방법으로 카테고리별 이진 특성을 만드는 원-핫-인코딩 기법이 있다. 한 특성만 1이고 나머지는 모두 0으로 만드는 방법이다.
- 원-핫-인코더의 출력은 사이파이(SciPy) 희소 행렬(sparse matrix)로 나타난다. 희소행렬은 0이 아닌 원소의 위치만 저장한다.


```python
# 사용자 정의 변환기 만들기
from sklearn.base import BaseEstimator, TransformerMixin

# 열 인덱스 처리
col_names = "total_rooms", "total_bedrooms", "population", "households"
rooms_ix, bedrooms_ix, population_ix, households_ix = [housing.columns.get_loc(c) for c in col_names]

class CombinedAttributesAdder(BaseEstimator, TransformerMixin):
    def __init__(self, add_bedrooms_per_room=True):
        self.add_bedrooms_per_room = add_bedrooms_per_room
    def fit(self, X, y=None):
        return self
    def transform(self, X):
        rooms_per_household = X[:rooms_ix] / X[:households_ix]
        population_per_household = X[:population_ix] / X[:, households_ix]
        if self.add_bedrooms_per_room:
            bedrooms_per_room = X[:bedrooms_ix] / X[:rooms_ix]
            return np.c_[X, rooms_per_household, population_per_household, bedrooms_per_room]

attr_adder = CombinedAttributesAdder(add_bedrooms_per_room=False)
housing_extra_attribs = attr_adder.transform(housing.to_numpy())
```
- 특성 조합으로 새롭운 특성을 만들어서 추가하는 작업이다.
- "bedrooms_per_room" 특성을 추가할지 말지 선택할 수 있다.

```python
# Dataframe(열 이름이 있는)으로 복원
housing_extra_attribs = pd.DataFrame(
    housing_extra_attribs,
    columns=list(housing.columns)+["rooms_per_household", "population_per_household"],
    index=housing.index)


# 변환 파이프라인 작성 - 데이터 전처리 일련의 과정을 통합
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

num_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy="median")),
    ('attribs_adder', CombinedAttributesAdder()),
    ('std_scaler', StandardScaler()),
])

housing_num_tr = num_pipeline.fit_transform(housing_num)

# full pipeline (범주형 데이터와 함께)
from sklearn.compose import ColumnTransformer

num_attribs = list(housing_num)
cat_attribs = ["ocean_proximity"]

full_pipeline = ColumnTransformer([
    ("num", num_pipeline, num_attribs),
    ("cat", OneHotEncoder(), cat_attribs)
])

housing_prepared = full_pipeline.fit_transform(housing)
```
- 머신러닝 알고리즘에 데이터를 넣기 위해 데이터 전처리 작업을 파이프라인으로 작성하였다. 일련의 데이터 전처리 과정을 통합한 것이다.
- 특성 스케일링(feature scaling)은 데이터에 적용해야 할 가장 중요한 변환이다. 머신러닝 알고리즘은 입력 숫자 특성들의 스케일이 많이 다르면 잘 작동하지 않기 때문이다.
- 모든 특성의 범위를 같도록 만들어주는 방법으로 min-max 스케일링과 표준화(standardization)가 널리 사용된다.
- Pipeline은 연속된 단계를 나타내는 이름/추정기 쌍의 목록을 입력으로 받는다. 마지막 단계에는 변환기와 추정기를 모두 사용할 수 있고 그 외에는 모두 변환기여야 한다.(fit_transform() 메서드를 가지고 있어야 한다.)
- 파이프라인의 fit() 메서드를 호출하면 모든 변환기의 fit_transform() 메서드를 순서대로 호출하면서 한 단계의 출력을 다음 단계의 입력으로 전달한다. 마지막 단계에서는 fit() 메서드만 호출한다.
- 위 예에서는 마지막 추정기가 변환기 StandardScaler이므로 파이프라인이 데이터에 대해 모든 변환을 순서대로 적용하는 transform() 메서드를 가지고 있다.


## 모델 선택과 훈련

```python
# ------ 모델 선택과 훈련 ------
# LinearRegression
from sklearn.linear_model import LinearRegression

lin_reg = LinearRegression()
lin_reg.fit(housing_prepared, housing_labels)

from sklearn.metrics import mean_squared_error

housing_predictions = lin_reg.predict(housing_prepared)
lin_mse = mean_squared_error(housing_labels, housing_predictions)
lin_rmse = np.sqrt(lin_mse)
```
- 위 모델의 경우 모델이 너무 단순해서 훈련 데이터에 과소적합된다.

```python
# DecisionTree
from sklearn.tree import DecisionTreeRegressor

tree_reg = DecisionTreeRegressor(random_state=42)
tree_reg.fit(housing_prpared, housing_labels)

housing_predictions = tree_reg.predict(housing_prepared)
tree_mse = mean_squared_error(housing_labels, housing_predictions)
tree_rmse = np.sqrt(tree_mse)
```
- 위 모델은 반대로 훈련 데이터에 과대적합된 것처럼 보인다.

```python
# RandomForest
from sklearn.ensemble import RandomForestRegressor

forest_reg = RandomForestRegressor(n_estimators=100, random_state=42)
forest_reg.fit(housing_prepared, housing_labels)

housing_predictions = forest_reg.predict(housing_prepared)
forest_mse = mean_squared_error(housing_labels, housing_predictions)
forest_rmse = np.sqrt(forest_mse)
```
- 특성을 무작위로 선택해서 많은 결정 트리를 만들고 그 예측을 평균내는 방식으로 작동하는 모델이다.
- 여러 다른 모델을 모아서 하나의 모델을 만드는 것을 앙상블 학습이라고 하며 머신러닝 알고리즘의 성능을 극대화하는 방법 중 하나이다.

```python
# 교차 검증을 사용한 평가
from sklearn.model_selection import cross_val_score

scores = cross_val_score(tree_reg, housing_prepared, housing_labels,
                        scoring="neg_mean_squared_error", cv=10)
tree_rmse_scores = np.sqrt(-scores)

lin_scores = cross_val_score(lin_reg, housing_prepared, housing_labels,
                            scoring="neg_mean_squared_error", cv=10)
lin_rmse_scores = np.sqrt(-lin_scores)

forest_scores = cross_val_score(forest_reg, housing_prepared, housing_labels,
                                scoring="neg_mean_squared_error", cv=10)
forest_rmse_scores = np.sqrt(-forest_scores)
```
- 교차검증(cross-validation): 훈련 세트를 더 작은 훈련 세트와 검증 세트로 나누어, 더 작은 훈련 세트에서 모델을 훈련시키고 검증 세트로 모델을 평가하는 방법이다.
- 사이킷런의 k-겹 교차 검증(k-fold cross-validation) 기능을 사용할 수 있다. 이때, 사이킷런의 교차 검증 기능은 scoring 매개변수에 낮을수록 좋은 비용함수가 아니라 클수록 좋은 효용함수를 기대한다. 따라서 음수값을 계산하는 neg_mean_squared_error 함수를 사용한다.
- 위 코드는 10(cv=10)개의 서브셋으로 훈련세트를 무작위로 분할한다. 따라서 10번의 검증데이터를 가지고 모델을 평가하고 10개의 평가 점수가 담긴 배열이 결과로 반환된다.

## 모델 세부 튜닝

```python
# ------ 모델 세부 튜닝 ------
# 그리드 탐색 - 하이퍼파라미터 후보를 미리 지정
from sklearn.model_selection import GridSearchCV

param_grid = [
    # 12(3x4)개의 하이퍼파라미터 조합을 시도
    {'n_estimators': [3, 10, 30], 'max_features': [2, 4, 6, 8]},
    # bootstrap은 False로 하고 6(2x3)개의 조합을 시도
    {'bootstrap': [False], 'n_estimators': [3, 10], 'max_features': [2, 3, 4]},
]

forest_reg = RandomForestRegressor(random_state=42)
# 다섯 개의 폴드로 훈련하면 총 (12+6)*5=90번의 훈련이 일어난다.
grid_search = GridSearchCV(forest_reg, param_grid, cv=5,
                            scoring='neg_mean_squared_error',
                            return_train_score=True)
grid_search.fit(housing_prepared, housing_labels)

# 최상의 파라미터 조합
grid_search.best_params_
grid_search.best_estimator_

# 그리드 탐색에서 테스트한 하이퍼파라미터 조합의 점수를 확인
cvres = grid_search.cv_results_
for mean_square, params in zip(cvres["mean_test_score"], cvres["params"]):
    print(np.sqrt(-mean_square), params)
```
- 시도해볼 만한 하이퍼파라미터 값을 지정하면, 모든 하이퍼파라미터 조합에 대해 교차 검증을 사용해 평가할 수 있는 기법이다.


```python
# 랜덤 탐색 - 각 반복마다 하이퍼파라미터에 임의의 수를 대입하여 지정한 횟수만큼 평가
from sklearn.model_selection import RandomizedSearchCV
from scipy.stats import randint

params_distribs = {
    'n_estimators': randint(low=1, high=200),
    'max_features': randint(low=1, high=8),
}

forest_reg = RandomForestRegressor(random_state=42)
rnd_search = RandomizedSearchCV(forest_reg, params_distribs, n_iter=10, cv=5,
                                scoring="neg_mean_squared_error", random_state=42)
rnd_search.fit(housing_prepared, housing_labels)

# 랜덤 탐색에서 테스트한 하이퍼파라미터 조합의 점수를 확인
cvres = rnd_search.cv_results_
for mean_score, params in zip(cvres["mean_test_score"], cvres["params"]):
    print(np.sqrt(-mean_score), params)

```
- 하이퍼파라미터 탐색 공간이 큰 경우 랜덤 탐색을 이용할 수 있다.
- 랜덤 탐색은 가능한 모든 조합을 시도하는 대신 각 반복마다 하이퍼파라미터에 임의의 수를 대입하여 지정한 횟수만큼 평가한다. 
- 예를 들어, 랜덤 탐색을 1000번 반복하면 하이퍼파라미터 각기 다른 1000개의 값을 탐색한다. 

## 테스트 세트로 시스템 평가하기

```python
# ------ 테스트 세트로 시스템 평가하기 ------
final_model = grid_search.best_estimator_

X_test = strat_test_set.drop("median_house_value", axis=1)
y_test = strat_test_set["median_house_value"].copy()

X_test_prepared = full_pipeline.transform(X_test)
final_predictions = final_model.predict(X_test_prepared)

final_mse = mean_squared_error(y_test, final_predictions)
final_rmse = np.sqrt(final_mse)
```

```python
# 테스트 RMSE에 대한 95% 신뢰구간 계산
from scipy import stats

confidence = 0.95
squared_errors = (final_predictions - y_test)**2
np.sqrt(stats.t.interval(confidence, len(squared_errors) - 1,
        loc=squared_errors.mean(), scale=stats.sem(squared_errors)))
```
- 일반화 오차의 95% 신뢰 구간을 계산한다.



#### [참고자료]
- 핸즈온 머신러닝 2판