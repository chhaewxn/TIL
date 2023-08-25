## 📝 금융데이터 활용 핀테크 AI 모델링 구현 실습 - 보험료 예측하기

### 다중회귀 모형
- 앞의 예제들은 물고기의 너비, 길이만 고려했으나, 이번에는 물고기의 두께까지 고려한다,
- 변수(특성)을 추가하고, 각각 제곱해서 새로운 특성을 만든다. 이러한 특성을 뽑아내는 작업을 '특성공학'이라고 함.

```python
import pandas as pd

df = pd.read_csv('https://bit.ly/perch_csv_data')
perch_full = df.to_numpy()
print(perch_full)

import numpy as np

perch_weight = np.array(
    [5.9, 32.0, 40.0, 51.5, 70.0, 100.0, 78.0, 80.0, 85.0, 85.0,
     110.0, 115.0, 125.0, 130.0, 120.0, 120.0, 130.0, 135.0, 110.0,
     130.0, 150.0, 145.0, 150.0, 170.0, 225.0, 145.0, 188.0, 180.0,
     197.0, 218.0, 300.0, 260.0, 265.0, 250.0, 250.0, 300.0, 320.0,
     514.0, 556.0, 840.0, 685.0, 700.0, 700.0, 690.0, 900.0, 650.0,
     820.0, 850.0, 900.0, 1015.0, 820.0, 1100.0, 1000.0, 1100.0,
     1000.0, 1000.0]
     
from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(perch_full, perch_weight, random_state=42)
     )
```
![](https://velog.velcdn.com/images/chhaewxn/post/7959bbd2-95c2-44a1-8cde-0fc5f898493c/image.png)

### 사이킷런 변환기
Scikit-Learn은 사용이 간편하고 일관된 인터페이스를 제공하여, 다양한 알고리즘을 효과적으로 사용하고 모델을 구축하며 평가할 수 있도록 도와주는 라이브러리

## 실습 - 보험료 예측
### 0. 데이터 불러오기 
```python
import pandas as pd

insurance = pd.read_csv('https://raw.githubusercontent.com/fintech-data/Revolution/main/data/insurance.csv')
insurance.head()
```

### 1. 데이터 전처리
```python
insurance.shape
insurance.info()
```

#### 1-1. 결측치 제거 
```python
# 결측치 없음
insurance.isnull().sum()
```

#### 1-2. 이상치 탐지
```python
insurance.describe()

import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.patheffects as path_effects
%matplotlib inline

bplot= sns.boxplot(data=insurance)
# charges의 값이 너무 커서 나머지 값들이 제대로 보이지 않으므로 하나하나 그려보자
```
![](https://velog.velcdn.com/images/chhaewxn/post/6f424897-623e-4b32-9649-515bbec47a25/image.png)

```python
insurance.head()
bplot= sns.boxplot(data= insurance[['age', 'bmi']])
bplot= sns.boxplot(data= insurance[['children']])
bplot= sns.boxplot(data= insurance[['charges']])
```
![](https://velog.velcdn.com/images/chhaewxn/post/6af5fa77-e195-40aa-b6b0-ecd5efe8c984/image.png)
![](https://velog.velcdn.com/images/chhaewxn/post/e7114fe9-b7c8-41e8-927f-fa549142cef2/image.png)
![](https://velog.velcdn.com/images/chhaewxn/post/3a0b15af-8469-4e24-9f3f-525f85b11799/image.png)

### 결론 : charges와 BMI에 이상치 값들이 있는 것을 확인

- 기본 :  x < Q1 - IQR or X > Q3 + IQR 값들을 제거
- 데이터에 따라 이상치가 필요하다고 판단하여 제거하지 않는 경우도 있음
- 보험의 경우 이상치가 있는 데이터가 필요하기에 이상치를 제거하지 않고 로그변환을 통해 일정부분 해결하자

### 2. 탐색적 자료분석(EDA)
#### 2-1. 범주형

```python
insurance.head()
insurance['sex'].value_counts().plot.bar()
insurance['children'].value_counts().plot.bar()
insurance['smoker'].value_counts().plot.bar()
insurance['region'].value_counts().plot.bar()
```

#### 2-2. 범주형 변수와 y와의 관계
- 범주형 & 연속형 : bar plot, box plot 등 

```python
# 남성일수록 보험료가 비싸다
sns.barplot(x='sex', y='charges', data=insurance)

# 자녀에 따른 보혐료의 차이는 미비하나 2~3명일 경우 보험료가 가장 비싸다.
sns.barplot(x='children', y='charges', data=insurance)

# Q. 5명일 때 특히 보험료가 작게 나온다 -> 이는 데이터 자체가 작아서이다. 위의 bar plot으로도 확인 가능

#자녀수가 4, 5일 때 데이터가 적다.
insurance['children'].value_counts()

# region에 의한 차이는 미비하다 남동쪽이 다른 지역에 비해 보험료가 비싸다
sns.barplot(x='region', y='charges', data=insurance)
```
![](https://velog.velcdn.com/images/chhaewxn/post/cde365f3-ba85-41e8-bf5d-b77232e2e0e2/image.png)

#### 가설 1. 남동부 지역의 BMI가 더 높다
- 확인: boxplot 그리자
```python
bplot= sns.boxplot(y = 'bmi', x= 'region', data = insurance)

# 실제로 남동쪽의 BMI가 더 높은것을 확인 할 수있다.
# 가설 성립!
```

### 2-3. 연속형

```python
import warnings
warnings.filterwarnings('ignore')

# 20대가 가장 많다
sns.distplot(insurance['age'])

# 정규분포 모형을 띄며 bmi 30~35 사이가 가장 많다.
sns.distplot(insurance['bmi'])

# 종속변수 y : 오른쪽으로 꼬리가 길다 -> log 변환
sns.distplot(insurance['charges'])


import numpy as np
# 종속변수 y : 오른쪽으로 꼬리가 길다 -> log 변환 -> 꼬리가 사라짐
sns.distplot(np.log(insurance['charges']))
# y -> log(y)를 사용하여 회귀분석

```
![](https://velog.velcdn.com/images/chhaewxn/post/60e158d8-ecea-4bc4-8c71-329a66c423d1/image.png)

### 2-4. 연속형 & 종속변수와의 관계
- 연속형 & 연속형 : 상관관계
```python
corr = insurance.corr(method = 'pearson')
corr

df_heatmap = sns.heatmap(corr, cbar = True, annot = True, annot_kws={'size' : 20}, fmt = '.2f', square = True, cmap = 'Greens')

# 종속변수(보험료)와의 상관관계의 경우, 나이 > BMI 순으로 상관관계가 높은 것을 알 수 있다
```

### 3. 모델링
- 회귀 예측 모델링 : Regression, RandomForest, SVM

#### 3-1. 회귀분석
```python
from sklearn.linear_model import LinearRegression

insurance_data = pd.get_dummies(insurance)
# 범주형 변수를 이진 형태로 변환하는 원-핫 인코딩 많이 사용됨

X= insurance_data[['age', 'bmi', 'children', 'sex_female', 'sex_male',
       'smoker_no', 'smoker_yes', 'region_northeast', 'region_northwest',
       'region_southeast', 'region_southwest']]
y = np.log(insurance_data['charges'])

from numpy import log as ln
x=ln(10) # 파이썬에서 log -> ln로그로 돼 있어서, ln을 지수 log10으로 변환하는 것이 필요

insurance_data["sex_male"]

from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=30)

# 단순회귀분석 모형 객체 생성
lr = LinearRegression()
lr.fit(X_train, y_train)

# train의 R^2 값
lr.score(X_train, y_train)

# test의 R^2 값
lr.score(X_test, y_test)

y_predict = lr.predict(X_test)

plt.scatter(y_test, y_predict, alpha=0.4)
plt.xlabel("Actual Value")
plt.ylabel("Predicted Value")
plt.title("MULTIPLE LINEAR REGRESSION")
plt.show()

y1 = insurance_data['charges']

from sklearn.model_selection import train_test_split
X_train, X_test, y1_train, y1_test = train_test_split(X, y1, test_size=0.30, random_state=30)
                           
# 단순회귀분석 모형 객체 생성
lr = LinearRegression()
lr.fit(X_train, y1_train)

# train의 R^2 값
lr.score(X_train, y1_train)

lr.score(X_test, y1_test)

```

![](https://velog.velcdn.com/images/chhaewxn/post/585241a8-fe81-4847-8e90-9a5dc8421b0d/image.png)

![](https://velog.velcdn.com/images/chhaewxn/post/5e6753e8-4e7e-4ab1-b27e-7c49a7b9ba09/image.png)


**+ 근접회귀 모델 만들기**

```python
from sklearn.neighbors import KNeighborsRegressor

knr = KNeighborsRegressor()

knr.fit(X_train, y_train)

print(knr.score(X_test, y_test))
```
➡️ 결과 확인해보면, 약 0.3644 로 정확도가 떨어지는 것을 알 수 있음

### 3-1-1. 비선형 회귀

```python
from sklearn.preprocessing import PolynomialFeatures
poly_features = PolynomialFeatures(degree=2, include_bias=False)
X_poly = poly_features.fit_transform(X_train)

lin_reg = LinearRegression()
lin_reg.fit(X_poly, y_train)

lin_reg.score(X_poly, y_train)

lin_reg.intercept_, lin_reg.coef_
X_test_poly = poly_features.transform(X_test)
y_predict = lin_reg.predict(X_test_poly)

plt.scatter(y_test, y_predict, alpha=0.4)
plt.xlabel("Actual Value")
plt.ylabel("Predicted Value")
plt.title("MULTIPLE LINEAR REGRESSION")
plt.show()
```

**➕ 모델 평가 지표 RMSE **

![](https://velog.velcdn.com/images/chhaewxn/post/b0f0ce4b-d63e-4fd9-8fd7-85a7d6909c22/image.png)

- 회귀 모델의 성능을 평가하기 위한 대표적인 평가 지표 중 하나 
- RMSE는 모델이 예측한 값과 실제 관측값 간의 차이를 평균화하고 제곱한 뒤, 이를 다시 제곱근을 취한 값!
- 작은 값일수록 모델의 예측이 더 정확하다

### 3-2. RandomForest
머신러닝에서 많이 사용되는 앙상블(Ensemble) 학습 알고리즘 중 하나로, 분류(Classification)와 회귀(Regression) 문제에 모두 사용할 수 있는 강력한 모델이다. 랜덤 포레스트는 여러 개의 의사결정트리(Decision Tree)를 앙상블하여 더 강력하고 안정적인 예측 모델을 구축하는데 사용된다.

```python
from sklearn.ensemble import RandomForestRegressor

rf = RandomForestRegressor() # 분류 트리(default) 객체 생성
rf.fit(X=X_train, y=y_train)

y_predict = rf.predict(X = X_test)

RMSE = mean_squared_error(y_test, y_predict)**0.5
RMSE
# RMSE가 0.4로 낮아지는 것을 볼 수있다
```

### 3-3. SVM
SVM(서포트 벡터 머신)은 머신러닝에서 지도 학습 알고리즘 중 하나로, 주로 분류(Classification)와 회귀(Regression) 문제에 사용되는 강력한 모델. SVM은 데이터를 고차원 공간으로 매핑하여 클래스를 분리하는 초평면(hyperplane)을 찾는 방식으로 작동한다.

```python
from sklearn.svm import SVR #회귀는 SVR

svr = SVR(kernel='linear', C=1.0, epsilon=0.1)
svr.fit(X_train, y_train)
y_predict = svr.predict(X_test)

RMSE = mean_squared_error(y_test, y_predict)**0.5
RMSE
# RMSE가 0.70으로 매우 커졌다.

from sklearn.svm import SVR #회귀는 SVR

svr = SVR(kernel='poly', C=1.0, epsilon=0.1)
svr.fit(X_train, y_train)

y_predict = svr.predict(X_test)

RMSE = mean_squared_error(y_test, y_predict)**0.5
RMSE
```

📌 결론 : RMSE가 가장 작은 비선형회귀일 경우 가장 보험료 예측을 잘한다
