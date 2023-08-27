# 📝 금융데이터 활용 핀테크 AI 모델링 구현 - 의사결정나무
## 의사결정나무
의사결정나무(Decision Tree)는 데이터를 분석하여 의사 결정 과정을 시각화하고 이해하기 쉽도록 하는 머신러닝 알고리즘 중 하나이다. 이는 나무 구조로 데이터를 분할하며, 각 분할 영역에서의 예측을 수행하는 방법을 제공한다. 이러한 나무 구조는 의사 결정 과정을 시각화하기에 용이하며, 데이터의 특성과 클래스를 기반으로 여러 개의 분기점(노드)과 결정 규칙을 사용하여 데이터를 분할한다.

의사결정나무의 기본 아이디어는 데이터 특성을 사용하여 연속적으로 분할하여 개별 노드를 생성하고, 이 노드들을 통해 예측을 수행하는 것이다. 각 노드에서 데이터는 그 특성의 값을 기반으로 다수의 클래스 중 하나로 할당된다. 각 노드는 특성을 선택하는 규칙과 분할 기준에 따라 생성되며, 이를 통해 데이터의 클래스나 결과를 예측하거나 분류할 수 있다.

## 설명하기 쉬운 모델과 어려운 모델
### 결정트리
```python
#결정트리를 만들어서 모형을 만든다 : 스무고개 하는 방식

from sklearn.tree import DecisionTreeClassifier

dt = DecisionTreeClassifier(random_state=42)

#여기서도 random_statef를 사용했는데 그 이유는?

dt.fit(train_scaled, train_target)

print(dt.score(train_scaled, train_target))
print(dt.score(test_scaled, test_target))

#분류 과정을 시각화해서 보여준다.
import matplotlib.pyplot as plt
from sklearn.tree import plot_tree

plt.figure(figsize=(10,7))
plot_tree(dt)
plt.show()
```

![](https://velog.velcdn.com/images/chhaewxn/post/97b347de-e6de-44db-b7b9-570d02a6d1e5/image.png)

## 가지치기
```python
#max_depth=3 (매개변수를 3로 준다 : 로트노투 하나를 제외하고 3개 더 그려줌]

dt = DecisionTreeClassifier(max_depth=3, random_state=42)
dt.fit(train_scaled, train_target)

print(dt.score(train_scaled, train_target))
print(dt.score(test_scaled, test_target))

#그림으로 보면서 노드의 단계를 확인

plt.figure(figsize=(20,15))
plot_tree(dt, filled=True, feature_names=['alcohol', 'sugar', 'pH'])
plt.show()
```
![](https://velog.velcdn.com/images/chhaewxn/post/c3b94f0b-f75c-41a2-9f57-e81d2d19ca1b/image.png)

```python
dt = DecisionTreeClassifier(max_depth=9, random_state=42)
dt.fit(train_input, train_target)

print(dt.score(train_input, train_target))
print(dt.score(test_input, test_target))

dt = DecisionTreeClassifier(max_depth=9, random_state=42)
dt.fit(train_scaled, train_target)

print(dt.score(train_scaled, train_target))
print(dt.score(test_scaled, test_target))

#교차검증
from sklearn.model_selection import cross_validate

scores = cross_validate(dt, train_input, train_target)
print(scores)

import numpy as np

print(np.mean(scores['test_score']))

plt.figure(figsize=(20,15))
plot_tree(dt, filled=True, feature_names=['alcohol', 'sugar', 'pH'])
plt.show()
```
![](https://velog.velcdn.com/images/chhaewxn/post/d006d805-68d1-4a07-bad7-016c0ddef328/image.png)
