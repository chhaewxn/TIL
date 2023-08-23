## 머신러닝의 문제: 분류의 문제
### 센서 데이터의 분류
- 새로운 센서 데이터가 수집된 경우, 장치의 오작동 여부 예측
분류 문제: 2개의 데이터를 가장 잘 구별하는 선형 판별식을 찾는 과정
> w1x1 + w2x2 = b

### 머신러닝 모델: K-MEANS CLUSTERING
- 주어진 데이터를 K개의 클러스터로 묶는 알고리즘
- 각 클러스터 중심에서 거리 차이의 분산을 최소화하는 방식으로 동작

**구현**
1. 무작위로 k개의 클러스터를 나눔 (k개의 seed point를 선정)
2. 각 seed point와 가까운 점들끼리 k개의 클러스터링 수행
3. 각 클러스터의 Center를 다시 구함
4. Center와 가까운 점들끼리 다시 k개의 클러스터링 수행

3->4->3-> ... : Loop(center 값이 변함이 없을 때까지 수행)
➡️ 거리 차이의 분산을 계산

N회의 iteration에서 가장 낮은 거리 차이의 분산을 보이는 경우를 채택

![](https://velog.velcdn.com/images/chhaewxn/post/567ae13b-4c8f-44fc-9109-91739c515d21/image.png)


### 흐름도
![](https://velog.velcdn.com/images/chhaewxn/post/0ba6dfd3-86d2-469e-b7b4-53b9ed60311a/image.png)

금융데이터에서는 주로 기계학습에서 지도학습(분류, 회귀), 비지도학습(클러스터링), 강화학습 순서로 많이 사용된다.

## 분류모형(Classification Model) 실습 - 여행 보험금 청구 여부 분류

```python
import pandas as pd
import warnings
warnings.filterwarnings('ignore')

travel_insurance = pd.read_csv("https://raw.githubusercontent.com/fintech-data/Revolution/main/data/travel%20insurance.csv")

travel_insurance.head()
```

![](https://velog.velcdn.com/images/chhaewxn/post/41c416a5-2476-4d7b-aa47-62c0636cb83e/image.png)

```python
travel_insurance.rename(columns={ 'Agency Type':'Agency_Type', 'Distribution Channel':'Distribution_Channel', 'Product Name':'Product_Name',
       'Net Sales':'Net_Sales', 'Commision (in value)':'Commision_in_value'
       },inplace=True)
# 여기서 변수명에 괄호 및 공백 제거 후에 언더바 사용
```

**변수명**
- Agency: str, 보험 판매점
- Agency Type: str, 판매점 형태
- Distribution Channel: str, 판매 채널
- Product Name : str, 판매 상품 종류
- Duration: str, 보험기간
- Destination: str, 여행국가
- Net Sales: int, 순마진
- Commision (in value): float, 수수료
- Gender: str, 성별
- Age: int, 나이
- Claim : 보험금 청구

### 1. 데이터 전처리
```python
travel_insurance.shape
travel_insurance.info() # 데이터 속성 확인 가능
```
결과

```sql
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 63326 entries, 0 to 63325
Data columns (total 11 columns):
 #   Column                Non-Null Count  Dtype  
---  ------                --------------  -----  
 0   Agency                63326 non-null  object 
 1   Agency_Type           63326 non-null  object 
 2   Distribution_Channel  63326 non-null  object 
 3   Product_Name          63326 non-null  object 
 4   Claim                 63326 non-null  object 
 5   Duration              63326 non-null  int64  
 6   Destination           63326 non-null  object 
 7   Net_Sales             63326 non-null  float64
 8   Commision_in_value    63326 non-null  float64
 9   Gender                18219 non-null  object 
 10  Age                   63326 non-null  int64  
dtypes: float64(2), int64(2), object(7)
memory usage: 5.3+ MB
```

#### 1-1. 결측치 제거
```python
travel_insurance.isnull().sum() #Gender에 결측치가 매우 많음
travel_insurance['Gender'].unique() # 성별을 모르는 경우가 매우 많다.
# array(['F', 'None', 'M'], dtype=object)

# History가 없는 경우 존재 -> NaN 값을 'None'이라는 모른다는 표시의 문자열로 변환
travel_insurance['Gender'] = travel_insurance['Gender'].fillna('None')
travel_insurance.isnull().sum()
```

#### 1-2. 이상치 탐지
```python
travel_insurance.describe()

import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.patheffects as path_effects
%matplotlib inline

bplot= sns.boxplot(data=travel_insurance) # DURATION에 매우 큰 이상치가 몰려있다.
```
![](https://velog.velcdn.com/images/chhaewxn/post/c20d3c6d-8027-4904-8b0d-c44b9bbe83f1/image.png)

```python
bplot= sns.boxplot(data= travel_insurance[['Duration']])
```

![](https://velog.velcdn.com/images/chhaewxn/post/f04e34e7-b973-444c-b806-f3789f86d3f6/image.png)

```python
bplot= sns.boxplot(data= travel_insurance[['Net_Sales']])
```

![](https://velog.velcdn.com/images/chhaewxn/post/762baee9-7667-4e4a-9d39-2b243ddc6606/image.png)

```python
bplot= sns.boxplot(data= travel_insurance[['Commision_in_value']])
```

![](https://velog.velcdn.com/images/chhaewxn/post/7543247e-31d8-45c7-8a6c-bbdaf61555b2/image.png)

```python
bplot= sns.boxplot(data= travel_insurance[['Age']])
```

![](https://velog.velcdn.com/images/chhaewxn/post/59a53607-2ecf-4391-9f10-9aa5d36609dd/image.png)

>데이터에 이상치가 매우 많음을 알 수 있음!
그러나 보험금 데이터의 경우 이상치가 보험금 청구에 영향을 미칠 수 있으므로 그대로 진행, 만약 성능이 안나온다면 이상치 제거 이후에 진행

📌 CHECK : 데이터에 이상치로 보이는 값들이 다수 존재 하지만 실제 고객 데이터 값으로 보고 그대로 진행, 만약에 이상치가 있다면 다음의 코드를 실행해서 이상치를 처리에 활용할 수 있음
```python
import numpy as np

def get_outlier(df=None, column=None, weight=1.5):
    # target 값과 상관관계가 높은 열을 우선적으로 진행
    q25 = np.percentile(df[column].values, 25)
    q75 = np.percentile(df[column].values, 75)

    IQR = q75 - q25
    IQR_weight = IQR*weight

    lowest = q25 - IQR_weight
    highest = q75 + IQR_weight

    outlier_idx = df[column][ (df[column] < lowest) | (df[column] > highest) ].index

    return outlier_idx
```

## 2. 탐색적 자료분석 (EDA)
### 2-1. 범주형(빈도 분석)
```python
plt.figure(figsize=(15,10))
for i in range(4):
    plt.subplot(2,2,1) 
    sns.countplot(x = 'Claim', data = travel_insurance)
    plt.title('Claim')
```
![](https://velog.velcdn.com/images/chhaewxn/post/c2c65c0c-6bbe-4ba6-b002-0a229adddbe7/image.png)

```python
plt.figure(figsize=(15,10))
for i in range(4):
    plt.subplot(2,2,1)
    sns.countplot(x = 'Agency_Type', data = travel_insurance)
    plt.title('Agency_Type')
```
![](https://velog.velcdn.com/images/chhaewxn/post/d9c76b61-cc65-4f1f-9b88-d1fc4e6f4b7e/image.png)

```python
plt.figure(figsize=(15,10))
for i in range(4):
    plt.subplot(2,2,1) #오탈자수정 i->1
    sns.countplot(x = 'Product_Name', data = travel_insurance)
    plt.title('Product_Name ')
```
![](https://velog.velcdn.com/images/chhaewxn/post/c5e9863c-bded-4855-8f64-0f96cd027286/image.png)

```python
plt.figure(figsize=(15,10))
for i in range(4):
    plt.subplot(2,2,1)
    sns.countplot(x = 'Distribution_Channel', data = travel_insurance)
    plt.title('Distribution_Channel')
 ```
 ![](https://velog.velcdn.com/images/chhaewxn/post/e5cc3c09-1135-4c5f-be02-55903b8ca1f5/image.png)

```python
plt.figure(figsize=(15,10))
for i in range(4):
    plt.subplot(2,2,1)
    sns.countplot(x = 'Gender', data = travel_insurance)
    plt.title('Gender')
 ```
 ![](https://velog.velcdn.com/images/chhaewxn/post/bae0d8fa-e6ec-4238-bb88-4840ccc18275/image.png)

```python
plt.figure(figsize=(15,10))

wedges, texts = plt.pie(travel_insurance['Destination'].value_counts(),
                                  labels = travel_insurance['Destination'].unique(),
                                  shadow = True,
                                  textprops = dict(color ="magenta"))


plt.pie(travel_insurance['Destination'].value_counts(),labels=travel_insurance['Destination'].unique())
plt.title('Destination')
```
![](https://velog.velcdn.com/images/chhaewxn/post/cd006f3c-3ff5-43e6-b88f-b55733937879/image.png)

### 2-2. 종속변수 y
```python
plt.figure(figsize=[10,10])
sorted_counts= travel_insurance.Claim.value_counts()
plt.pie(sorted_counts, explode=(0.1,0),labels=['No', 'Yes'],
       colors=['#009ACD', '#ADD8E6'], autopct='%1.0f%%',
       shadow=False, startangle=0,
       pctdistance=1.2,labeldistance=1.4)
plt.axis('equal')
plt.title("Number of Travel Claimed vs Non-Claimed")


# : 불균형이 상당히 심한 불균형 데이터
## 1) 평가지표를 f1 score를 사용
## 2) 머신러닝 불균형 해소 방법인 SMOTE 이용
```
![](https://velog.velcdn.com/images/chhaewxn/post/7dd7e986-2aca-4d1b-a076-19ad46ad88a7/image.png)

### 2-3. 연속형
```python
travel_insurance.head(2)
# 2개의 결과값만 출력됨 

# 오른쪽으로 꼬리가 긴 분포를 띈다.
sns.distplot(travel_insurance['Duration'])
```
![](https://velog.velcdn.com/images/chhaewxn/post/b71b25fa-eaa1-4c03-b4a4-0aeae743828f/image.png)

```python
# 0인 경우가 대부분이다
sns.distplot(travel_insurance['Commision_in_value'])
```
![](https://velog.velcdn.com/images/chhaewxn/post/bbd2166b-08c3-48d8-91ee-d75aabdb6a06/image.png)

### 2-4. 연속형 독립변수 상관관계
```python
corr = travel_insurance.corr(method = 'pearson')
corr
df_heatmap = sns.heatmap(corr, cbar = True, annot = True, annot_kws={'size' : 20}, fmt = '.2f', square = True, cmap = 'Blues')
```
![](https://velog.velcdn.com/images/chhaewxn/post/95629bf0-7069-406e-bc59-1da2bf5d2344/image.png)

![](https://velog.velcdn.com/images/chhaewxn/post/80e14d21-d4ce-47ab-89ac-31b738c9cdee/image.png)

```python
# hue 옵션으로 구분해서 볼수도 있다. (시간이 다소 걸림)
sns.pairplot(data=travel_insurance,hue="Claim")
plt.show()
plt.close()
```
![](https://velog.velcdn.com/images/chhaewxn/post/d8c03acd-0406-4173-8e09-14c4ef98c768/image.png)

## 3. 모델링
이진분류 모델링 : 근접이웃모형 (보험금 청구 여부, Claim이 0 또는 1)

### 3-1. 모델링을 위한 전처리
```python
# 일단 범주형 변수를 원핫인코딩 한다
# 예) 남성, 여성 -> 0, 1로 바꿈

from sklearn import preprocessing
# 데이터 전처리 과정에 사용되는 모듈 

label_encoder1 = preprocessing.LabelEncoder()
travel_insurance['Agency']= label_encoder1.fit_transform(travel_insurance['Agency'])
# 레이블 인코더 객체 생성 및 적용

label_encoder2 = preprocessing.LabelEncoder()
travel_insurance['Agency_Type']= label_encoder2.fit_transform(travel_insurance['Agency_Type'])

label_encoder3 = preprocessing.LabelEncoder()
travel_insurance['Distribution_Channel']= label_encoder3.fit_transform(travel_insurance['Distribution_Channel'])

label_encoder4 = preprocessing.LabelEncoder()
travel_insurance['Product_Name']= label_encoder4.fit_transform(travel_insurance['Product_Name'])

label_encoder5 = preprocessing.LabelEncoder()
travel_insurance['Claim']= label_encoder5.fit_transform(travel_insurance['Claim'])

label_encoder6 = preprocessing.LabelEncoder()
travel_insurance['Destination']= label_encoder6.fit_transform(travel_insurance['Destination'])

# 일단 범주형 변수를 원핫인코딩 한다
# 예) 남성, 여성 -> 0, 1로 바꿈

from sklearn import preprocessing
label_encoder1 = preprocessing.LabelEncoder()
travel_insurance['Agency']= label_encoder1.fit_transform(travel_insurance['Agency'])

label_encoder2 = preprocessing.LabelEncoder()
travel_insurance['Agency_Type']= label_encoder2.fit_transform(travel_insurance['Agency_Type'])

label_encoder3 = preprocessing.LabelEncoder()
travel_insurance['Distribution_Channel']= label_encoder3.fit_transform(travel_insurance['Distribution_Channel'])

label_encoder4 = preprocessing.LabelEncoder()
travel_insurance['Product_Name']= label_encoder4.fit_transform(travel_insurance['Product_Name'])

label_encoder5 = preprocessing.LabelEncoder()
travel_insurance['Claim']= label_encoder5.fit_transform(travel_insurance['Claim'])

label_encoder6 = preprocessing.LabelEncoder()
travel_insurance['Destination']= label_encoder6.fit_transform(travel_insurance['Destination'])

# 일단 범주형 변수를 원핫인코딩 한다
# 예) 남성, 여성 -> 0, 1로 바꿈

from sklearn import preprocessing
label_encoder1 = preprocessing.LabelEncoder()
travel_insurance['Agency']= label_encoder1.fit_transform(travel_insurance['Agency'])

label_encoder2 = preprocessing.LabelEncoder()
travel_insurance['Agency_Type']= label_encoder2.fit_transform(travel_insurance['Agency_Type'])

label_encoder3 = preprocessing.LabelEncoder()
travel_insurance['Distribution_Channel']= label_encoder3.fit_transform(travel_insurance['Distribution_Channel'])

label_encoder4 = preprocessing.LabelEncoder()
travel_insurance['Product_Name']= label_encoder4.fit_transform(travel_insurance['Product_Name'])

label_encoder5 = preprocessing.LabelEncoder()
travel_insurance['Claim']= label_encoder5.fit_transform(travel_insurance['Claim'])

label_encoder6 = preprocessing.LabelEncoder()
travel_insurance['Destination']= label_encoder6.fit_transform(travel_insurance['Destination'])

```

**보험금 청구한 데이터 세트만 뽑아서 보자! **

```python
#claim이 1인 즉 보험금을 청구한 데이터 세트
X_test_claim_1 = X_test[y_test['Claim'] == 1]
y_test_claim_1 = y_test[y_test['Claim'] == 1]

#보험금을 청구한 케이스만가지고 예측해 본다
kn.predict(X_test_claim_1)
```

### 원인 분석
- True / True의 비율이 너무 커서 오류 발생
![](https://velog.velcdn.com/images/chhaewxn/post/3fe6dd4c-ad80-4277-8a59-3c9a65a7dc78/image.png)

## 균형상태
- claim=1, 전체 보험금 청구가 50% 수준인 데이터 셋
- 이진분류 모델링 : 근접이웃모형 (보험금 청구 여부, Claim이 0 또는 1)

```python
# 일단 범주형 변수를 원핫인코딩 한다
# 예) 남성, 여성 -> 0, 1로 바꿈

from sklearn import preprocessing
label_encoder1 = preprocessing.LabelEncoder()
travel_insurance['Agency']= label_encoder1.fit_transform(travel_insurance['Agency'])

label_encoder2 = preprocessing.LabelEncoder()
travel_insurance['Agency_Type']= label_encoder2.fit_transform(travel_insurance['Agency_Type'])

label_encoder3 = preprocessing.LabelEncoder()
travel_insurance['Distribution_Channel']= label_encoder3.fit_transform(travel_insurance['Distribution_Channel'])

label_encoder4 = preprocessing.LabelEncoder()
travel_insurance['Product_Name']= label_encoder4.fit_transform(travel_insurance['Product_Name'])

label_encoder5 = preprocessing.LabelEncoder()
travel_insurance['Claim']= label_encoder5.fit_transform(travel_insurance['Claim'])

label_encoder6 = preprocessing.LabelEncoder()
travel_insurance['Destination']= label_encoder6.fit_transform(travel_insurance['Destination'])

column_names = ["Agency", "Agency_Type", "Distribution_Channel","Product_Name","Duration","Destination","Net_Sales","Commision_in_value","Age","Claim"]
travel_insurance = travel_insurance.reindex(columns=column_names)

y= travel_insurance.iloc[:,[-1]]
X= travel_insurance.drop(y.columns,axis = 1)

# ! pip install imblearn
# 불균형 데이터를 다루기 위한 패키지
from imblearn.over_sampling import SMOTE

sm = SMOTE(random_state=42)
X_sm, y_sm = sm.fit_resample(X, y)
print(f'''Shape of X before SMOTE: {X.shape}
Shape of X after SMOTE: {X_sm.shape}''')
print('\nBalance of positive and negative classes (%):')
y_sm.value_counts(normalize=True) * 100

X_sm.columns

#데이터 분할
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X_sm, y_sm, test_size=0.2, random_state=42)

from sklearn.neighbors import KNeighborsClassifier
kn = KNeighborsClassifier()

kn.fit(X_train, y_train)
kn.score(X_test, y_test)

#claim이 1인 즉 보험금을 청구한 데이터 세트
X_test_claim_1 = X_test[y_test['Claim'] == 1]
y_test_claim_1 = y_test[y_test['Claim'] == 1]

X_test_claim_1
y_test_claim_1

#보험금을 청구한 케이스만가지고 예측해 본다
k1=kn.predict(X_test_claim_1)
print(k1)

df_k1 = pd.DataFrame(k1, columns=['k1'])

# 클래스별 데이터 개수 계산
counts = df_k1['k1'].value_counts()

# 1의 비율 계산
ratio = counts[1] / len(df_k1) * 100

# 막대 그래프 그리기
plt.bar(counts.index, counts.values)

# 비율 정보 텍스트 추가
plt.text(0.5, max(counts.values), f"Ratio of 1: {ratio:.2f}%", ha='center')

# 그래프에 제목과 축 레이블 추가
plt.title("Distribution of k1")
plt.xlabel("k1")
plt.ylabel("Count")

# 그래프 보여주기
plt.show()

```

![](https://velog.velcdn.com/images/chhaewxn/post/0033a891-ad94-4cb4-8394-67923df8a425/image.png)


