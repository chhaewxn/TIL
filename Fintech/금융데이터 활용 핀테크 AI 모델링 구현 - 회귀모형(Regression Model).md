## 📝 금융데이터 활용 핀테크 AI 모델링 구현 실습 4일차 - 회귀모형(Regression Model)

### 1-1. 데이터 입력

```python
import numpy as np
#데이터를 배열(array)로 입력하기 위해서 numpy라는 알고리즘을 np라는 이름으로 해서 입력합니다.

#농어의 길이 입력
perch_length = np.array([8.4, 13.7, 15.0, 16.2, 17.4, 18.0, 18.7, 19.0, 19.6, 20.0, 21.0,
       21.0, 21.0, 21.3, 22.0, 22.0, 22.0, 22.0, 22.0, 22.5, 22.5, 22.7,
       23.0, 23.5, 24.0, 24.0, 24.6, 25.0, 25.6, 26.5, 27.3, 27.5, 27.5,
       27.5, 28.0, 28.7, 30.0, 32.8, 34.5, 35.0, 36.5, 36.0, 37.0, 37.0,
       39.0, 39.0, 39.0, 40.0, 40.0, 40.0, 40.0, 42.0, 43.0, 43.0, 43.5,
       44.0])

#입력값 확인
perch_length

#농어 숫자 확인
len(perch_length)

#농어의 무게
perch_weight = np.array([5.9, 32.0, 40.0, 51.5, 70.0, 100.0, 78.0, 80.0, 85.0, 85.0, 110.0,
       115.0, 125.0, 130.0, 120.0, 120.0, 130.0, 135.0, 110.0, 130.0,
       150.0, 145.0, 150.0, 170.0, 225.0, 145.0, 188.0, 180.0, 197.0,
       218.0, 300.0, 260.0, 265.0, 250.0, 250.0, 300.0, 320.0, 514.0,
       556.0, 840.0, 685.0, 700.0, 700.0, 690.0, 900.0, 650.0, 820.0,
       850.0, 900.0, 1015.0, 820.0, 1100.0, 1000.0, 1100.0, 1000.0,
       1000.0])

#입력값 확인
perch_weight

#농어 숫자 확인
len(perch_weight)
#숫자가 맞는지 확인, 총 56개가 돼야 한다.

```

## 1-2. 데이터를 한 번에 보기 : 산점도

```python
import matplotlib.pyplot as plt
#차트를 그리는 함수를 plt로 이름을 줄여서 불러옵니다.

plt.scatter(perch_length, perch_weight)
#농어의 길이를 x축 농어의 무게를 y축으로 하는 차트를 그립니다.
```
![](https://velog.velcdn.com/images/chhaewxn/post/f7b90509-a63d-4cf4-b019-4cffe34b0e94/image.png)


```python
#공분산
a1=np.cov(perch_length,perch_weight)

a=np.cov(perch_length,perch_weight)[0,1]
#[0,1]를 빼고 입력하면 행렬로 나온다.
print(a)

#상관계수
b=np.corrcoef(perch_length,perch_weight)[0,1]
#[0,1]를 빼고 입력하면 행렬로 나온다.
print(b)

plt.scatter(perch_length, perch_weight)
#농어의 길이를 x축 농어의 무게를 y축으로 하는 차트를 그립니다.
plt.xlabel('length')
#차트의 x축을 길이(length)라고 이름을 붙입니다.
plt.ylabel('weight')
#차트의 y축을 무게(weight)라고 이름을 붙입니다.

plt.scatter(perch_weight, perch_length)
#농어의 길이를 y축 농어의 무게를 x축으로 하는 차트를 그립니다.

import numpy as np

#공분산
a=np.cov(perch_length, perch_weight)[0,1]
#[0,1]를 빼고 입력하면 행렬로 나온다.
print(a)

#상관계수
b=np.corrcoef(perch_length, perch_weight)[0,1]
#[0,1]를 빼고 입력하면 행렬로 나온다.
print(b)
```

**➕ 공분산**
: 공분산은 두 변수 간의 관계를 나타내는 통계적 개념으로, 두 변수가 함께 어떻게 변하는지를 측정하는데 사용됨. 공분산의 값이 양수라면 두 변수는 같은 방향으로 함께 움직이는 경향이 있으며, 음수라면 반대 방향으로 움직이는 경향이 있다. 공분산의 절댓값이 클수록 두 변수 간의 관계가 강하게 나타난다.

**➕ 상관계수**
: 상관계수는 두 변수 간의 관계를 -1부터 1 사이의 값으로 나타내는 통계적 지표로, 주로 피어슨 상관계수(Pearson Correlation Coefficient)가 많이 사용된다. 상관계수는 공분산을 각 변수의 표준편차로 나누어 정규화한 값이다.

- 상관계수가 1에 가까우면, 두 변수는 양의 선형 관계를 가지고 있다
한 변수가 증가하면 다른 변수도 증가
- 상관계수가 -1에 가까우면, 두 변수는 음의 선형 관계를 가지고 있다
한 변수가 증가하면 다른 변수는 감소
- 상관계수가 0에 가까우면, 두 변수 간에는 선형적인 관계가 거의 없다

상관계수는 공분산과 달리 단위에 영향을 받지 않아서 변수들의 관계를 파악하기 더 편리하지만 두 변수 간의 관계가 선형적일 때의 관계만을 측정한다는 한계가 있다!

### 1-3. 머신러닝 코드 구현을 위한 데이터 셋 분리

```python
#농어의 데이터를 훈련세트/테스트 세트로 구분하기 위해서 이 기능이 train_test_split을 불러옵니다.
from sklearn.model_selection import train_test_split

#훈련을 위한 데이터, 검증(test)를 위한 데이터(길이)와 무게(target)로 데이터를 나누고, 랜덤42이라고 지정해 이를 실행합니다.
train_input,test_input,train_target,test_target= train_test_split(perch_length, perch_weight, random_state=42)

#1,2,3,4로 구성된 원소를 배열(array)형태로 지정합니다.
test_array=np.array([1,2,3,4])

#배열(array)형태로 어떻게 저장됐는지 보겠습니다.
test_array

#4x1 행렬과 같은 것이군요
print(test_array.shape)

#배열을 2x2로 바꿉니다.
test_array1=test_array.reshape(2,2)

#바꾼 배열의 형태를 보기
print(test_array1.shape)

#배열의 크기에 -1를 입력할 경우 : 나머지를 원소의 개수로 채운다
train_input1=train_input.reshape(-1,1)
test_input1=test_input.reshape(-1,1)

#바뀐 배열 형식을 출력한다.
print(train_input1.shape, test_input1.shape)

```

### 1-4. 결정계수

```python
from sklearn.neighbors import KNeighborsRegressor

knr=KNeighborsRegressor()
knr.fit(train_input1, train_target)
knr.score(test_input1, test_target)

print(knr.score(test_input1, test_target))
```

**➕ K-Nearest Neighbors(K-NN) 알고리즘**
: K-NN 회귀는 주어진 데이터 포인트에 대해 회귀 분석을 수행하는데 사용되며, 이웃 데이터 포인트들의 값들을 평균이나 가중 평균으로 계산하여 예측값 생성. 주어딘 데이터에서 가장 가까운 이웃 데이터 포인트들을 기반으로 예측을 수행하므로, 데이터의 패턴이나 구조를 학습하지 않고 주어진 데이터에 따라 예측을 수행함.


### 1-5. 과대 적합 VS 과소 적합
```python
#훈련한 모델의 결정계수를 봅니다.
print(knr.score(train_input1, train_target))

#근접점의 개수를 3개로 설정합니다.
#근접점을 3~10까지 올려가면서 정확도 계산
knr.n_neighbors=3
#모델을 근접점 3개를 반영해 다시 훈련시킵니다.
knr.fit(train_input1, train_target)

#새롭게 훈련한 결과치의 점수를 봅니다.데이터는 훈련세트
print(knr.score(train_input1, train_target))

print(knr.score(test_input1,test_target))
#테스트 세트로 해보니까 97%
```

### 2-1. 선형 회귀 모형

```python

#선형회귀 모형을 위해서 LinearRegression이라는 함수를 불러옵니다.
from sklearn.linear_model import LinearRegression

lr = LinearRegression()
# 선형 회귀 모델 훈련
lr.fit(train_input1, train_target)

# 50cm 농어에 대한 예측
print(lr.predict([[50]]))
#앞 모델의 예측값(1033g)과 비교해봅니다.

print(lr.coef_, lr.intercept_)
#축의 기울기와 y절편을 봅니다.

# 훈련 세트의 산점도를 그립니다
plt.scatter(train_input, train_target)
# 15에서 50까지 1차 방정식 그래프를 그립니다.
# (선형으로 선을 긋고 x값을 넣으면 y값을 예측합니다.)
plt.plot([15, 50], [15*lr.coef_+lr.intercept_, 50*lr.coef_+lr.intercept_])
# 50cm 농어 데이터
plt.scatter(50, 1241.8, marker='^')
plt.xlabel('length')
plt.ylabel('weight')
plt.show()

print(lr.score(train_input1, train_target))
print(lr.score(test_input1, test_target))
#훈련용 세트로 구한 예측도와 테스트 세트로 구한 예측도를 봅니다.
```

![](https://velog.velcdn.com/images/chhaewxn/post/513d4b20-90dc-48e5-90ae-5bf95021fdcf/image.png)

### 2-2. 다항회귀 (비선형 회귀 모형)
```python
train_poly = np.column_stack((train_input ** 2, train_input))
test_poly = np.column_stack((test_input ** 2, test_input))
#2차 방정식으로 예측


print(train_poly.shape, test_poly.shape)

lr = LinearRegression()
lr.fit(train_poly, train_target)

print(lr.predict([[50**2, 50]]))
#왜 50의 제곱을 모형에 집어넣을까요?

print(lr.coef_, lr.intercept_)
#훈련한 모델의 기울기와 y절편을 출력

# 구간별 직선을 그리기 위해 15에서 49까지 정수 배열을 만듭니다
point = np.arange(15, 50)
# 훈련 세트의 산점도를 그립니다
plt.scatter(train_input, train_target)
# 15에서 49까지 2차 방정식 그래프를 그립니다
plt.plot(point, 1.01*point**2 - 21.6*point + 116.05)
# 50cm 농어 데이터
plt.scatter([50], [1574], marker='^')
plt.xlabel('length')
plt.ylabel('weight')
plt.show()

```

![](https://velog.velcdn.com/images/chhaewxn/post/6b5c7ee2-6761-4e92-88e5-d9e6f24a3120/image.png)

