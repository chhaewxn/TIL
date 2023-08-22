## 📝 금융데이터 활용 핀테크 AI 모델링 구현 실습 - 금융과 인공지능
## 머신러닝이란?

### 📌  머신러닝이란?
- 데이터를 기반으로 패턴을 학습하여 결과를 추론하는 것
- 데이터와 값만 있으면 인공지능이 스스로 패턴학습이 가능
- 이 패턴학습이 가능하도록 반복적으로 기계를 학습시키는 것.

### 📌  머신러닝과 기존 전통 프로그래밍의 차이
- 기존 전통적 프로그래밍 : 프로그램에 데이터를 입력하면 결과값을 출력하는 형식
- 머신러닝: 데이터와 출력값을 통해 프로그램을 학습시켜 새로운 데이터에 대해서 학습 가능하게 하는 형식.

![](https://velog.velcdn.com/images/chhaewxn/post/a083d717-b3d8-412b-91c9-4075f1c48ed6/image.png)

### 📌 머신러닝의 분류
1) 지도학습 :  정답(label) 이 있는 데이터를 활용해 데이터를 학습시키는 방법 
- 대표적으로 분류(Classification), 회귀(Regression)으로 나뉨

2) 비지도학습: 정답 없는 데이터를 컴퓨터 스스로 학습하여 숨겨진 의미, 패턴을 찾아내고 구조화 하는 방법 
- 예시) 군집분석(Clustering), 연관분석(Association Analysis),  PCA

3) 강화학습 : 주어진 환경에서 보상을 최대화 하도록 에이전트를 학습하는 기법 
- 예시) Q-Learning, 정책경사

### 📌 머신러닝 개발 절차

#### 1)  비지니스 이해 (Business Understanding)
- 비지니스 관점에서 프로젝트의 목적과 요구사항을 이해하기 위한 단계
- 도메인 지식을 데이터 분석을 위한 문제 정의로 변경
- 초기 프로젝트 계획 수립

#### 2) 데이터 수집 (Data Understanding)
- 데이터 수집
    - e.g. OpenAPI, 크롤링 등

#### 3) 데이터 전처리 (Data Preprocessing)
- 결측치 제거
- 이상치 탐지

#### 4) 탐색적 자료 분석 (EDA)
- 모델링 전에 데이터에 대해 다양한 방법을 통해서 관찰하고 이해하는 과정
- EDA 과정을 충분히 거치며 중요변수 파생 변수 등을 파악한 후  모델링을 해야함.

#### 5) 모델링 (Modeling)
- 다양한 머신러닝 모델 학습

#### 6) 평가 (Evaluation)
- 분석 모형이 프로젝트의 목적에 부합한지 평가
- 데이터 마이닝 결과를 수용할것인지 최종적으로 판단

#### 7) 전개 (Deployment)
- 완성된 분석 모형을 업무에 적용하는 과정

### 📌 머신러닝 모형 적합성 검증

#### 과적합 (Overfitting)
- 모델이 훈련데이터에 너무 적합하여 새로운 데이터에 오히려 더 성능이 떨어지는 경우

#### 과소적합(Underfitting)
- 모델이 아직 덜 적합한 상태. 더 최적화 가능한 상태를 의미
![](https://velog.velcdn.com/images/chhaewxn/post/cceb0a30-04f7-47dc-8e9f-f207c5c9ad9a/image.png)

--- 
### 🔧 1일차 추가 실습
**FRED (Federal Reserve Economic Data) API를 사용해서 데이터를 받기**

1) Fred API를 Colab에 설치하기
```python
pip install fredapi
```

2) API를 활용해서 데이터 받기
```python
#차트를 그리는 라이브러리 사용
#기준금리 데이터를 받아오는 라이브러리 사용
import matplotlib.pyplot as plt
import pandas as pd
from fredapi import Fred

# FRED API 키 입력
fred = Fred(api_key='b2182a80ba352b4be7486642fe6444c7')

# 기준금리 데이터를 가져옵니다. (시작일, 종료일)
data_interest = fred.get_series('DFF', start_date='2017-11-09', end_date='2023-06-30')


# 데이터프레임으로 변환합니다. (한글, 영어 모두 가능)
df_interest = pd.DataFrame(data_interest, columns=['기준금리'])
df_interest.index.name = '날짜'

# 시작일 이후의 데이터만 필터링합니다.
df_interest = df_interest[df_interest.index >= '2017-11-09']


# 기준 금리 데이터 : 미리보기
df_interest

# 기준금리 데이터 출력
print(df_interest)

```

3) 차트로 만들기
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

4) 응용 '기간'별 구분하여 시각화
```python
import pandas as pd
import matplotlib.pyplot as plt
import yfinance as yf
from fredapi import Fred

# 1. Load PayPal's stock price data
paypal_data = yf.download('PYPL', start="2017-11-09", end="2023-06-30")

# 2. Define the start and end dates for each phase
phase1_start = '2017-11-09'
phase1_end = '2019-07-31'
phase2_start = '2019-07-31'
phase2_end = '2020-03-15'
phase3_start = '2020-03-15'
phase3_end = '2022-03-17'
phase4_start = '2022-03-17'
phase4_end = '2023-06-30'

# 3. 날짜 구간을 가져옴
phase1_data = paypal_data.loc[phase1_start:phase1_end]
phase2_data = paypal_data.loc[phase2_start:phase2_end]
phase3_data = paypal_data.loc[phase3_start:phase3_end]
phase4_data = paypal_data.loc[phase4_start:phase4_end]

# 4. 기준금리의 데이터를 가져옴
fred = Fred(api_key='b2182a80ba352b4be7486642fe6444c7')
interest_rate_data = fred.get_series('DFF', start_date="2017-11-09", end_date="2023-06-30")

# 5. 페이팔 주가를 3의 구간으로 나눔
phase1_data.index = pd.to_datetime(phase1_data.index)
phase2_data.index = pd.to_datetime(phase2_data.index)
phase3_data.index = pd.to_datetime(phase3_data.index)
phase4_data.index = pd.to_datetime(phase4_data.index)
interest_rate_data.index = pd.to_datetime(interest_rate_data.index)

# 6. 각 영역에 대한 색깔을 지정
colors = ['blue', 'orange', 'green', 'red']

# 7. 불러온 데이터를 "차트"에 입력
plt.figure(figsize=(10, 6))
plt.subplot(2, 1, 1)
plt.plot(phase1_data.index, phase1_data['Close'], label='Time Zone 01', color=colors[0])
plt.plot(phase2_data.index, phase2_data['Close'], label='Time Zone 02', color=colors[1])
plt.plot(phase3_data.index, phase3_data['Close'], label='Time Zone 03', color=colors[2])
plt.plot(phase4_data.index, phase4_data['Close'], label='Time Zone 04', color=colors[3])
plt.xlabel("Date")
plt.ylabel("PayPal Stock Price")
plt.title("PayPal Stock Price - Phases")
plt.legend()

# 8. 기준금리 데이터도 위와 같이 진행
plt.subplot(2, 1, 2)
plt.plot(interest_rate_data.index, interest_rate_data, color='gray')
plt.xlabel("Date")
plt.ylabel("Interest Rate (%)")
plt.title("Federal Reserve Interest Rate - Phases")
plt.xlim(pd.to_datetime("2017-11-09"), pd.to_datetime("2023-06-30"))  # Set the x-axis limit for interest rate chart
plt.ylim(0, 8)  # Set the y-axis limit for interest rate chart

# Create colored background for each phase 구간별 배경색 변경
plt.axvspan(phase1_data.index[0], phase1_data.index[-1], color=colors[0], alpha=0.2)
plt.axvspan(phase2_data.index[0], phase2_data.index[-1], color=colors[1], alpha=0.2)
plt.axvspan(phase3_data.index[0], phase3_data.index[-1], color=colors[2], alpha=0.2)
plt.axvspan(phase4_data.index[0], phase4_data.index[-1], color=colors[3], alpha=0.2)

plt.tight_layout()
plt.show()
```

