# 📝 금융데이터 활용 핀테크 AI 모델링 구현 실습 - 로지스틱 회귀
> 참조 : https://heung-bae-lee.github.io/2020/04/03/machine_learning_06/

![](https://velog.velcdn.com/images/chhaewxn/post/2de0568f-bb23-4918-96e8-d67555b7d16b/image.png)'

## 데이터 준비하기
```python
# 사용할 데이터를 불러와서 저장하기
import pandas as pd

fish = pd.read_csv('https://bit.ly/fish_csv_data')
fish.head()

fish #데이터 확인해보기

#생선의 종류를 보고 싶다
#특정한 columes에 대해서 나올 수 있는 index값 목록을 보고 싶다.
print(pd.unique(fish['Species']))

#생선의 종류가 몇 가지인지 구한다
fish_num=pd.unique(fish['Species']) #생선의 index값 목록을 fish_num으로 저장
len(fish_num) #원소의 개수를 구한다
fish_num

#생선의 종류 : 타깃(target)
#나머지 값 : 입력데이터(input)
#데이터 프레임에서 5개의 열을 선택하고 이를 fish_input이라는 이름으로 저장
fish_input = fish[['Weight','Length','Diagonal','Height','Width']].to_numpy()

print(fish_input[:5]) #5개 행만 출력
fish_input #전체보기

#생선의 타깃데이터를 생성해서 데이터 프레임 형식으로 저장
#데이터 프레임으로 저장하기 위해서 to_numpy 입력
fish_target = fish['Species'].to_numpy()

#저장된 데이터 프레임 확인하기
fish_target

#데이터를 표준화 시켜서 전처리 합니다.
#훈련 데이터 세트 / 테스트(검증)데이터 세트 모두 변환합니다.

from sklearn.preprocessing import StandardScaler

ss = StandardScaler()
ss.fit(train_input,train_target)
train_scaled = ss.transform(train_input)
test_scaled = ss.transform(test_input)

```

## K-NN의 확률 예측
```python
from sklearn.neighbors import KNeighborsClassifier

#최근접 이웃의 개수를 3개로 지정해서 맞추기
#개수를 5, 7, 9로 해서 확률을 비교해보세요
kn = KNeighborsClassifier(n_neighbors=3)
# 가능하면 홀수(3,5,7...) -> 실제로 5, 7, 9
kn.fit(train_scaled, train_target)

#훈련용 데이터 세트의 정확도
print(kn.score(train_scaled, train_target))

#테스트(검즘용) 데이터 세트의 정확도
print(kn.score(test_scaled, test_target))
```

**➕ 다중 분류(Multi Class Classfication)**

이전의 도미/빙어의 문제 : 2개 중 하나 
지금의 문제 : 7개 중에 하나

이전의 도미/빙어의 문제 : 2개 중 하나이고, 0과1로 수치로 클래스를 분류 
지금의 문제 : 그냥 text그대로 분류

(순서의 문제) : 사전편찬식으로 순서가 자동으로 배열 (A부터 Z순서)

```python
# 사이킷런에 저장된 인덱스 값
print(kn.classes_)

# 참고 원래 순서
pd.unique(fish['Species'])

#5개 테스트(검증용) 샘플(5개)의 타깃값을 예측함
print(kn.predict(test_scaled[:5]))
```

## 로지스틱 회귀 
- 이진 분류(Binary Classification) 문제에 주로 사용되는 머신러닝 알고리즘으로 입력 특성(feature)을 기반으로 하나의 샘플이 두 개의 클래스 중 하나에 속할 확률을 예측하는 모델을 생성

로지스틱 회귀의 핵심 아이디어는 선형 회귀와 비슷하지만, 출력을 확률로 제한하고, 로그 오즈(log-odds)라고도 불리는 선형 함수를 변형한 형태를 사용함

(최근접 점 모형) 근접한 3개의 값가 가장 가까운 것을 고름 : 마케팅 팀에서 정확성을 가지고 보다 개선된 모형을 요청해서 로지스틱 회귀 모형을 사용해서 분류하기로 함

\z= a * weight + b * Length + c * Diagonal + d * Height + e * width + f

1) a * weight + b * Length + c * Diagonal + d * Height + e * width + f -> 구하면 임의의 x값(feature로 계산한 값)이 나온다

2) x를 시그모이드함수(특정 값이 나올 확률)로 바꿔 주는 과정을 거쳐 나온 z값(확률)

*   a, b, c, d, e는 계수 또는 가중치라고 하며, 선형 회귀모형의 계수와 동일함
*   z는 확률로 0부터 1사이의 값임

그런데 원래 z는 구간의 제한이 없는 값이지만 우리가 아래와 같이 시그모이드 함수(Sigmoid function 또는 Logistic function)을 사용해서 바꿈
![](https://velog.velcdn.com/images/chhaewxn/post/92e7e57d-50da-47be-9add-c4ed5859ae7b/image.png)

x축의 모든 값을 y축의 0과 1사이 값으로 변환시켜 주는 함수

*   z가 무한이 큰 음수일 경우에 이 함수는 0에 가깝게 됨
*   z가 무한하게 양수가 될 경우에는 1에 가까워 짐
*   z가 0이 될 때에는 0.5가 됨
![](https://velog.velcdn.com/images/chhaewxn/post/e8ffe2f8-c259-4dd7-a677-b202766eb347/image.png)

```python
#numpy를 이용해서 시그모이드 함수 만들기

import numpy as np
import matplotlib.pyplot as plt

z = np.arange(-5, 5, 0.1)
phi = 1 / (1 + np.exp(-z))

plt.plot(z, phi)
plt.xlabel('z')
plt.ylabel('phi')
plt.show()
```
![](https://velog.velcdn.com/images/chhaewxn/post/cb1deda5-4b6e-4900-a2fd-3401c06f2be9/image.png)

## 로지스틱 회귀로 이진 분류 수행하기
- 최근접 회귀 : 임의로 설정한 근접점에 가장 가까운 생선 확률
- 로지스틱 회귀 : 각 생선이 나올 확률을 모두 계산

```python
#블리언 인덱싱(Boolean Indexing -> True/False 값을 전달해 행을 선택)

#A부터 E까지 있는 값 중에서 TRUE가 있는 값만 골라낸다.
char_arr = np.array(['A', 'B', 'C', 'D', 'E'])
print(char_arr[[True, False, True, False, False]])

#도미와 빙어를 골라내는 훈련세트에 적용

#==로 도미와 빙어에 대한 행만 골라냄
bream_smelt_indexes = (train_target == 'Bream') | (train_target == 'Smelt')
train_bream_smelt = train_scaled[bream_smelt_indexes]
target_bream_smelt = train_target[bream_smelt_indexes]

from sklearn.linear_model import LogisticRegression

#로지스틱 회귀 모형
lr = LogisticRegression()
lr.fit(train_bream_smelt, target_bream_smelt)

#처음 5개의 샘플만 예측
print(lr.predict(train_bream_smelt[:5]))

#예측 확률을 보여줌
print(lr.predict_proba(train_bream_smelt[:5]))

#행렬의 컬럼 값에 대응되는 부분
print(lr.classes_)

#회귀모형의 계수
# z= a * weight + b * Length + c * Diagonal + d * Height + e * width + f
print(lr.coef_, lr.intercept_)

#각 모형의 z값을 계산
decisions = lr.decision_function(train_bream_smelt[:5])
print(decisions)

#확률을 계산
from scipy.special import expit

print(expit(decisions))

```
![](https://velog.velcdn.com/images/chhaewxn/post/aae4816d-d70b-432a-a881-7d07f5a1d2d3/image.png)

## 로지스틱 회귀로 다중 분류 수행하기
```python
lr = LogisticRegression(C=100, max_iter=100000)
lr.fit(train_scaled, train_target)

print(lr.score(train_scaled, train_target))
print(lr.score(test_scaled, test_target))

#C값과 ITERATION 값을 바꿔서 정확도를 계산

print(lr.predict(test_scaled[:5]))

#5개 샘플에 대한 예측확률을 계산
#그런데 왜 7개 행이 계산?

proba = lr.predict_proba(test_scaled[:5])
print(np.round(proba, decimals=3))

print(lr.classes_)

#다중 분류의 선형방정식을 출력함
#5개의 특성을 사용해서 7개의 생선의 확률을 예측함
print(lr.coef_.shape, lr.intercept_.shape)

decision = lr.decision_function(test_scaled[:5])
print(np.round(decision, decimals=2))

#시그모이드 함수 : 하나의 선형 방정식의 출력 값을 0~1사이로 압축함
# softmax함수 : 여러개의 선행 방정식의 출력 값을 0~1사이로 압축하고 전체 합이 1이 되도록함
from scipy.special import softmax

proba = softmax(decision, axis=1)
print(np.round(proba, decimals=3))
```

