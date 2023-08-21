## 01-A 핀테크 비즈니스와 A.I 모델

**KNN**
가장 가까운 이웃들의 레이블을 기반으로 예측을 수행하는 알고리즘
특성을 찾는 알고리즘

활용 사례: 고객 분류, 개인화된 금융 서비스, 부정 거래 탐지 등
실습: 보험금 청구 모델, 금융 상품 이탈 고객 예측

**Regression**
입력과 출력 변수 간의 관계를 모델링하여 값을 예측하는 알고리즘
value를 찾는 알고리즘

활용 사례: 금융 시장 예측, 자산 가격 예측, 이자율 예측 등
실습: 보험료 계산 모델

**RNN**
순차적인 데이터를 처리하는 딥 러닝 모델로 시계열 데이터 예측과 자연어 처리에 적합함

활용 사례: 금융 시계열 데이터 예측, 고객 의견 분석 등
실습: 주가 예측 모델

주요 사용 모델
라이브러리: numpy, keras, theano, pandas, pytorch, tensor flow, scikit-learn, matplotib, scipy, seaborn

## 핀테크 비즈니스와 신용평가 모형
핀테크(Fintech)는 금융(Finance)과 기술(Technology)을 결합한 용어로, 금융 서비스 분야에서 혁신적인 기술과 솔루션을 의미

추천 시스템에서 주로 사용되는 기술
- CONTENT-BASED FILTERING : 아이템을 추천할 때 각 아이템의 특성과 사용자의 선호도를 기반으로 추천하는 방식으로 아이템의 내용이나 속성을 분석하여 사용자에게 유사한 아이템을 추천한다.
- COLLABORATIVE FILTERING : 사용자들의 과거 행동(구매 기록, 평가 등)을 기반으로 유사한 사용자나 아이템을 찾아서 추천하는 방식으로 사용자 간의 유사성을 파악하여 추천하는데 중점을 둔다.

차이점
- CONTENT-BASED FILTERING은 아이템의 내용이나 특성을 사용하여 추천하며, COLLABORATIVE FILTERING은 사용자 간의 상호작용 데이터를 사용하여 추천
- CONTENT-BASED FILTERING은 아이템의 속성과 사용자의 취향을 기반으로 하기 때문에 새로운 아이템에 대한 추천이 가능하고, COLLABORATIVE FILTERING은 사용자 간의 유사성을 파악하여 추천하기 때문에 새로운 아이템에 대한 추천이 어려울 수 있음
- CONTENT-BASED FILTERING은 아이템의 내용을 분석하는 과정이 필요하고, COLLABORATIVE FILTERING은 사용자 간의 유사성을 계산하는 과정이 필요함

#### 기술이 비즈니스를 만난다면 ?
![](https://velog.velcdn.com/images/chhaewxn/post/809a3dc4-3b05-47f6-b6e3-760a0972762f/image.png)


## 01차시_1일차 실습
### yfinance를 설치하여 주가 데이터 가져오기 

**1) 야후파이낸스 라이브러리 실행**
```python
import yfinance as yf
```


**2) 그래프 및 데이터프레임(엑셀형식의 데이터 작성) 지원 라이브러리 실행**
```python
import pandas as pd
import matplotlib.pyplot as plt
```

**3) 데이터를 받아오는 코드**
```python
# 페이팔 주식 데이터를 가져옵니다.
start_date = '2015-09-01'
end_date = '2023-06-30'
ticker = 'PYPL'
data_pypl = yf.download(ticker, start=start_date, end=end_date)

# 데이터프레임으로 변환합니다.
df_pypl = pd.DataFrame(data_pypl['Close'])
df_pypl.columns = [ticker + 'price']
df_pypl.index.name = 'date'

# 시작일 이후의 데이터만 필터링합니다.
df_pypl = df_pypl[df_pypl.index >= '2015-09-01']


# 기준 금리 데이터 : 미리보기
df_pypl
```

결과, 
![](https://velog.velcdn.com/images/chhaewxn/post/028a2828-11ff-46f4-bd10-6f8085d06028/image.png)
위와 같이 페이팔의 기준 금리 데이터 표가 나온다.

**4) 데이터를 저장하는 코드**
```python
# 페이팔 주가 데이터를 CSV 파일로 저장

# CSV 파일로 저장
from google.colab import files

df_pypl.to_csv('pypl.csv', encoding='utf-8-sig')

# 파일을 다운로드합니다.
files.download('pypl.csv')
```

**5) 차트를 그려주는 코드**
```python
# 차트(그래프) 작성
plt.figure(figsize=(10, 6))
plt.plot(df_pypl) # 그래프 작성 코드 
plt.title(ticker + ' price')
plt.xlabel('date')
plt.ylabel('price')
plt.grid(True)
plt.show()
```

### FRED (Federal Reserve Economic Data) API를 사용해서 데이터를 받기

**1) Fred API를 Colab에 설치하기**
```python
pip install fredapi
```

**2-1) API를 활용하여 데이터를 받아오기**
```python
#차트를 그리는 라이브러리 사용
#기준금리 데이터를 받아오는 라이브러리 사용
import matplotlib.pyplot as plt
import pandas as pd
from fredapi import Fred

# FRED API 키 입력
fred = Fred(api_key='******')

# 기준금리 데이터를 가져옵니다. (시작일, 종료일)
data_interest = fred.get_series('DFF', start_date='2017-11-09', end_date='2023-06-30')


# 데이터프레임으로 변환합니다. (한글, 영어 모두 가능)
df_interest = pd.DataFrame(data_interest, columns=['기준금리'])
df_interest.index.name = '날짜'

# 시작일 이후의 데이터만 필터링합니다.
df_interest = df_interest[df_interest.index >= '2017-11-09']


# 기준 금리 데이터 : 미리보기
df_interest
```

결과) 
![](https://velog.velcdn.com/images/chhaewxn/post/4e433caa-9dd0-4fc8-93d7-8a0e84797bf1/image.png)

**2-2) API를 활용하여 데이터를 저장하는 코드**
```python
# 기준금리 데이터를 CSV 파일로 저장

# CSV 파일로 저장합니다.
from google.colab import files

df_interest.to_csv('interest.csv', encoding='utf-8-sig')

# 파일을 다운로드합니다.
files.download('interest.csv')
```

**2-2) API를 활용하여 차트로 만들기**
```python
# 차트를 그립니다.
plt.figure(figsize=(10, 6))
plt.plot(df_interest)  #df_interest 대신에 "data_interest"를 입력하면 어떻게 달라질까요?
plt.title('usa_interest')
plt.xlabel('day')
plt.ylabel('interest(%)')
plt.grid(True)
plt.show()
```
결과)
![](https://velog.velcdn.com/images/chhaewxn/post/44adea27-c859-42d0-b4bf-80e84716ab5a/image.png)
