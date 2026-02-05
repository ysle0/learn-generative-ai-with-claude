# Chapter 02: 머신러닝 기초 개념

> **이 장의 대상 독자:** 매일 AI 도구를 사용하지만, 그 내부가 어떻게 동작하는지 모르는 개발자

---

## 핵심 키워드 요약

| 키워드 | 정의 |
|--------|------|
| **지도 학습 (Supervised Learning)** | 입력-출력 쌍으로 구성된 데이터를 통해 패턴을 학습하는 방법 |
| **비지도 학습 (Unsupervised Learning)** | 정답 없이 데이터 자체의 구조와 패턴을 발견하는 방법 |
| **강화 학습 (Reinforcement Learning)** | 보상 신호를 최대화하는 방향으로 행동을 학습하는 방법 |
| **특성 (Feature)** | 모델에 입력되는 개별 측정값 또는 속성 |
| **레이블 (Label)** | 모델이 예측해야 하는 정답 값 |
| **손실 함수 (Loss Function)** | 모델의 예측값과 실제 값 사이의 차이를 수치화하는 함수 |
| **과적합 (Overfitting)** | 훈련 데이터에 지나치게 맞춰져 새 데이터에 대한 일반화 능력이 떨어지는 현상 |
| **과소적합 (Underfitting)** | 모델이 데이터의 패턴을 충분히 학습하지 못한 상태 |
| **편향-분산 트레이드오프 (Bias-Variance Tradeoff)** | 모델의 단순성과 복잡성 사이에서 최적점을 찾는 균형 문제 |
| **교차 검증 (Cross-Validation)** | 데이터를 여러 번 나누어 모델을 반복 평가하는 기법 |

---

## 1. 머신러닝이란 무엇인가

### 전통적 프로그래밍 vs 머신러닝

개발자로서 우리는 보통 이런 방식으로 코드를 작성합니다.

```python
# 전통적 프로그래밍: 규칙을 직접 작성
if email.contains("무료") and email.sender not in whitelist:
    classify_as_spam()
elif email.contains("당첨") and email.has_link():
    classify_as_spam()
# ... 규칙이 끝없이 늘어남
```

규칙이 수천 개가 되면? 새 스팸 패턴마다 개발자가 규칙을 추가해야 합니다.
머신러닝은 이를 근본적으로 다르게 접근합니다.

```python
# 머신러닝 방식: 데이터로부터 규칙을 학습
model = learn_from(spam_examples, normal_examples)
result = model.predict(new_email)
```

**머신러닝(Machine Learning)** 은 "명시적으로 프로그래밍하지 않아도 데이터로부터
스스로 학습하는 시스템"입니다.

### 개발자를 위한 비유: 컴파일러처럼 생각하기

```
전통적 프로그래밍:  [ 규칙(코드) ] + [ 데이터 ]     --> [ 결과 ]
머     신  러  닝:  [ 데이터 ]     + [ 결과(정답) ] --> [ 규칙(모델) ]
```

컴파일러가 소스 코드를 실행 파일로 바꾸듯이, 머신러닝에서는 **데이터가 소스 코드**,
**학습 알고리즘이 컴파일러**, **모델이 실행 파일**입니다.

- 소스 코드(데이터)의 품질이 나쁘면 실행 파일(모델)도 나쁩니다.
- 컴파일러(알고리즘)를 잘 골라야 좋은 결과를 얻습니다.
- 한번 빌드(학습)하면, 실행 파일(모델)만으로 예측이 가능합니다.

---

## 2. 학습의 세 가지 패러다임

### 2.1 지도 학습 (Supervised Learning)

**"선생님이 정답을 알려주면서 가르치는 방식"** -- 입력(Feature)과 정답(Label)의 쌍으로 학습합니다.

```python
training_data = [
    ({"면적": 60, "층수": 3, "역세권": True},  3.5),   # (특성, 가격(억))
    ({"면적": 85, "층수": 10, "역세권": True}, 5.2),
    ({"면적": 40, "층수": 2, "역세권": False}, 1.8),
]
model = train(training_data)
model.predict({"면적": 70, "층수": 5, "역세권": True})  # --> 약 4.1억
```

지도 학습은 두 가지 주요 문제로 나뉩니다.

**분류 (Classification):** "어떤 범주에 속하는가?" -- 이메일 -> 스팸/정상, 코드 -> 버그 있음/없음

**회귀 (Regression):** "값이 얼마인가?" -- 아파트 정보 -> 예상 가격, 서버 메트릭 -> 응답 시간(ms)

> **개발자 팁:** API 응답이 카테고리(문자열)면 분류, 숫자면 회귀라고 생각하면 됩니다.

### 2.2 비지도 학습 (Unsupervised Learning)

**"정답 없이 데이터 자체에서 구조를 찾는 방식"**

```python
# 비지도 학습: 정답(label)이 없다
raw_data = [
    {"구매빈도": 30, "평균금액": 5000,  "최근방문": 1},
    {"구매빈도": 2,  "평균금액": 50000, "최근방문": 30},
]
clusters = find_groups(raw_data, num_groups=3)
# --> 그룹 A: 자주 소액 구매 / 그룹 B: 드물게 고액 구매 / 그룹 C: 이탈 위험
```

**군집화 (Clustering):** 비슷한 데이터끼리 자동 그룹핑 (사용자 세분화, 로그 패턴 분류)

```
  평균금액 ^
           |  B     B            A, B, C = 자동 발견된 군집
           |     B
           |  A  A  A     C
           |    A  A    C   C
           +---------------------> 구매빈도
```

**차원 축소 (Dimensionality Reduction):** 100개 특성을 핵심 2~3개로 압축.
대형 JSON에서 핵심 필드만 추출하는 것과 비슷합니다.

### 2.3 강화 학습 (Reinforcement Learning)

**"시행착오를 통해 보상을 최대화하는 방식"**

```
+----------+  행동(Action)   +-----------+
|  에이전트  | ------------> |    환경     |
|  (Agent)  | <------------ | (Environ.) |
+----------+  상태 + 보상   +-----------+
```

```python
for episode in range(10000):
    state = environment.reset()
    while not done:
        action = agent.choose_action(state)
        next_state, reward, done = environment.step(action)
        agent.learn(state, action, reward, next_state)
        state = next_state
```

대표 사례: 게임 AI (AlphaGo), 로봇 제어, LLM의 RLHF (ChatGPT가 유용한 답변을 하도록 학습)

---

## 3. 핵심 개념 깊이 이해하기

### 3.1 특성 (Feature)과 레이블 (Label)

데이터베이스 용어로 비유하면:

```
Features (입력 컬럼들)              Label (정답)
면적 | 층수 | 역세권 | 건축년도  --> 가격(억)
  60 |   3  |   1    |  2015    -->   3.5
  85 |  10  |   1    |  2020    -->   5.2

-- SQL로 생각하면:
SELECT 면적, 층수, 역세권, 건축년도 FROM apartments  -- Features
-- 모델이 예측할 대상: 가격 --> Label
```

- **특성 (Feature):** 모델에 입력되는 각 변수. DB 컬럼, API 요청 파라미터에 해당.
- **레이블 (Label):** 모델이 맞혀야 하는 정답. 타겟 컬럼, API 응답 값에 해당.

좋은 특성을 선정하는 **특성 공학(Feature Engineering)** 이 모델 성능에 결정적 영향을 미칩니다.

### 3.2 손실 함수 (Loss Function)

"모델이 얼마나 틀렸는지"를 숫자로 표현합니다. 단위 테스트의 assertion과 비슷합니다.

```python
# 단위 테스트: 정확히 일치해야 통과
assert actual_price == expected_price

# 머신러닝: 오차를 수치화 (더 유연함)
loss = (predicted_price - actual_price) ** 2

# 전체 데이터에 대한 평균 손실 (Mean Squared Error)
def mse_loss(predictions, actuals):
    total = sum((p - a) ** 2 for p, a in zip(predictions, actuals))
    return total / len(predictions)
```

| 문제 유형 | 손실 함수 | 직관적 의미 |
|-----------|-----------|-------------|
| 회귀 | MSE (Mean Squared Error) | 예측값과 실제값 차이의 제곱 평균 |
| 회귀 | MAE (Mean Absolute Error) | 차이의 절대값 평균 |
| 분류 | Cross-Entropy Loss | 예측 확률 분포와 실제 분포의 차이 |

### 3.3 경사 (Gradient)와 학습 과정

**경사(Gradient)** 는 손실을 줄이기 위해 파라미터를 어느 방향으로, 얼마나 조정할지 알려줍니다.

```
손실(Loss)
    ^
    |  *                      산 정상에서 공을 굴린다고 상상하세요.
    |   **                    공은 경사면을 따라 가장 낮은 곳으로
    |     ***                 굴러갑니다. 그것이 경사 하강법입니다.
    |        *****
    |             ******* <-- 최저점 (최적의 파라미터)
    +--------------------------------------> 파라미터 값
```

```python
def train_step(model, data, learning_rate=0.01):
    predictions = model.forward(data.features)
    loss = compute_loss(predictions, data.labels)
    gradients = compute_gradients(loss, model.parameters)

    for param, grad in zip(model.parameters, gradients):
        param = param - learning_rate * grad  # 핵심 한 줄!
    return loss
```

`learning_rate`(학습률): 너무 크면 최저점을 지나쳐 발산(빌드 실패),
너무 작으면 수렴이 매우 느림(빌드가 몇 시간 걸리는 상황).

---

## 4. 과적합과 과소적합

### 4.1 과적합 (Overfitting)

**"훈련 데이터를 외워버려서 새로운 데이터에 대응하지 못하는 상태"**

특정 테스트 케이스만 통과하도록 하드코딩한 코드와 같습니다.

```python
# 과적합: 데이터를 외워버림            # 일반화된 모델: 패턴을 학습
def predict_price(area):              def predict_price(area):
    if area == 60: return 3.5             return 0.06 * area + 0.1
    if area == 85: return 5.2
    if area == 40: return 1.8
    return ???  # 새 입력 대응 불가!
```

```
가격 ^
     |    o          o           o = 실제 데이터
     |   /\  o      /\
     |  /  \/  \   /  \         --- 과적합 (훈련 100%, 새 데이터 실패)
     | /        \ /
     |/ - - - - - - - - -       --- 적절한 학습 (훈련 90%, 새 데이터 85%)
     +----------------------------> 면적
```

### 4.2 과소적합 (Underfitting)

**"데이터의 패턴을 충분히 학습하지 못한 상태"** -- 모든 입력에 평균값만 반환하는 모델.

```python
def predict_price(area):
    return 3.5  # 면적이 뭐든 항상 같은 값!
```

### 4.3 진단 요약

| 상태 | 훈련 성능 | 검증 성능 | 개발자 비유 |
|------|-----------|-----------|-------------|
| 과소적합 | 낮음 | 낮음 | 테스트 실패, 실서비스도 실패 |
| 적절한 학습 | 높음 | 높음 | 테스트 통과, 실서비스 안정 |
| 과적합 | 매우 높음 | 낮음 | 테스트는 통과, 실서비스 장애 |

---

## 5. 편향-분산 트레이드오프 (Bias-Variance Tradeoff)

과적합/과소적합을 더 정밀하게 설명하는 프레임워크입니다.

- **편향 (Bias):** 모델이 너무 단순해서 생기는 체계적 오차. 높은 편향 = 과소적합.
- **분산 (Variance):** 훈련 데이터 변화에 민감한 불안정성. 높은 분산 = 과적합.

```
  높은 편향 / 낮은 분산     낮은 편향 / 높은 분산     낮은 편향 / 낮은 분산
  +----------+            +----------+            +----------+
  |          |            | *      * |            |          |
  |   * * *  |            |          |            |   * *    |
  |   * * *  |            |  *    *  |            |   ***    |
  |     ^    |            |    ^     |            |    ^     |
  +----------+            +----------+            +----------+
  (과녁에서 멀지만         (과녁 근처이지만         (과녁 중심에
   모여 있음)              흩어져 있음)             모여 있음 = 이상적!)
```

```
총 오차 = 편향^2 + 분산 + 줄일 수 없는 노이즈

오차 ^
     | \                  /
     |  \  편향          / 분산
     |   \             /
     |    \          /
     |     \--------/       <-- 최적의 모델 복잡도
     |       총 오차
     +---------------------------------> 모델 복잡도
         단순 <-----------> 복잡
```

**실무 가이드:** 너무 단순하면 복잡도를 올리고(편향 감소),
너무 복잡하면 정규화(regularization)나 데이터 추가로 분산을 줄입니다.

---

## 6. 데이터 분할: Train / Validation / Test

### 왜 데이터를 나누는가?

시험 문제를 미리 알고 공부하면 실력을 정확히 평가할 수 없듯이,
모델도 학습에 사용하지 않은 데이터로 평가해야 합니다.

```
전체 데이터셋 (예: 10,000건)
├── 훈련 세트 (Train Set):      70% = 7,000건  -- "수업 교재"
├── 검증 세트 (Validation Set):  15% = 1,500건  -- "모의고사"
└── 테스트 세트 (Test Set):      15% = 1,500건  -- "수능 본시험" (단 한 번만!)
```

```python
def split_data(data, train_ratio=0.7, val_ratio=0.15):
    random.shuffle(data)
    n = len(data)
    train_end = int(n * train_ratio)
    val_end = int(n * (train_ratio + val_ratio))
    return data[:train_end], data[train_end:val_end], data[val_end:]
```

| 세트 | 용도 | 개발 비유 |
|------|------|-----------|
| Train | 모델 학습 | 개발 환경 (dev) |
| Validation | 모델 튜닝 및 선택 | 스테이징 환경 (staging) |
| Test | 최종 성능 보고 | 프로덕션 환경 (production) |

**흔한 실수:** Validation으로 반복 튜닝하면 간접적으로 Validation에도 과적합됩니다.
이것이 별도 Test 세트가 필요한 이유입니다.

### 교차 검증 (Cross-Validation)

데이터가 적을 때 더 안정적인 평가를 위한 기법입니다.

```
K-Fold Cross-Validation (K=5)

Fold 1: [Val  ][Train][Train][Train][Train]  --> 0.85
Fold 2: [Train][Val  ][Train][Train][Train]  --> 0.83
Fold 3: [Train][Train][Val  ][Train][Train]  --> 0.87
Fold 4: [Train][Train][Train][Val  ][Train]  --> 0.84
Fold 5: [Train][Train][Train][Train][Val  ]  --> 0.86
                                    최종 성능 = 평균 = 0.85
```

모든 데이터가 한 번씩 검증에 사용되므로 데이터를 최대한 활용하며, 성능 추정이 안정적입니다.

---

## 7. 주요 알고리즘 훑어보기

### 7.1 선형 회귀 (Linear Regression)

가장 단순한 회귀 모델. 입력-출력 사이에 직선 관계를 가정합니다.

```python
# y = w1*x1 + w2*x2 + ... + b  (가격 = w1*면적 + w2*층수 + b)
def linear_regression_predict(features, weights, bias):
    return bias + sum(f * w for f, w in zip(features, weights))
# 학습 = 최적의 weights와 bias를 찾는 과정
```

```
가격 ^        * /
     |       */          y = 0.06x + 0.1
     |     * /
     |    /
     |  / *
     | /*
     +---------------------> 면적
```

장점: 해석 쉬움, 빠름, 특성별 영향력 파악 가능 / 한계: 비선형 관계 표현 불가

### 7.2 결정 트리 (Decision Tree)

if-else 문의 트리 구조. 개발자에게 가장 직관적인 알고리즘입니다.

```
                [면적 > 60?]
               /            \
             Yes             No
            /                  \
     [역세권?]              [층수 > 5?]
     /      \               /        \
   Yes      No            Yes        No
    |        |              |          |
 5.0억    3.8억          2.5억      1.5억
```

```python
def predict(sample, node):
    if node.is_leaf:
        return node.value
    if sample[node.feature] > node.threshold:
        return predict(sample, node.right)
    return predict(sample, node.left)
```

장점: 해석 쉬움, 비선형 관계 포착 가능 / 한계: 단일 트리는 과적합되기 쉬움

### 7.3 랜덤 포레스트 (Random Forest)

여러 결정 트리를 만들고 다수결로 결정합니다.
마이크로서비스에서 여러 서비스 결과를 종합하는 것과 유사합니다.

```
  트리1    트리2    트리3   ...   트리N
   |        |        |            |
  4.8억   5.1억   4.9억        5.0억
    \        \      /            /
     +---- 평균(투표) ----------+
              |
           4.95억 <-- 최종 예측
```

```python
def random_forest_predict(sample, trees):
    predictions = [tree.predict(sample) for tree in trees]
    return sum(predictions) / len(predictions)  # 회귀: 평균 / 분류: 다수결
```

장점: 과적합에 강함, 특성 중요도 파악 가능 / 한계: 해석 어려움, 느릴 수 있음

### 7.4 서포트 벡터 머신 (Support Vector Machine, SVM)

두 클래스를 가장 넓은 마진(margin)으로 분리하는 경계면을 찾습니다.

```
  특성2 ^
        |  o  o           x  x       o = 클래스 A, x = 클래스 B
        |   o   o  |    x    x       | = 결정 경계 (최대 마진)
        |  o    o  |     x  x
        +----------|--|---------------> 특성1
                   |<>|<>|
                   마진 마진
```

장점: 고차원 데이터에 효과적, 일반화 우수 / 한계: 대용량에서 느림, 커널 선택 필요

### 알고리즘 선택 가이드

```
데이터가 적고 특성이 많은가?
  ├── Yes --> SVM 또는 선형 회귀
  └── No
       ├── 해석 가능성이 중요한가?
       |    ├── Yes --> 결정 트리 또는 선형 회귀
       |    └── No  --> 랜덤 포레스트
       └── 데이터가 매우 큰가? (수백만 건 이상)
            └── Yes --> 랜덤 포레스트 또는 딥러닝 고려
```

---

## 8. 정리: 개발자의 멘탈 모델

```
1. 데이터 수집    SELECT * FROM ...     (원재료)
2. 특성 공학      transform(raw_data)    (전처리)
3. 데이터 분할    train / val / test     (dev/staging/prod)
4. 모델 선택      LinearRegression()     (프레임워크 선택)
5. 학습           model.fit(train)       (컴파일/빌드)
6. 평가           model.score(val)       (테스트 실행)
7. 튜닝           adjust(hyperparams)    (성능 최적화)
8. 최종 평가      model.score(test)      (출시 전 QA)
9. 배포           model.predict(new)     (프로덕션 배포)
```

### 이 장에서 기억할 것

1. **머신러닝은 데이터로부터 규칙을 자동으로 추출하는 것**입니다.
   전통적 프로그래밍이 규칙을 직접 작성하는 것과 대비됩니다.

2. **지도/비지도/강화 학습**은 정답 유무와 학습 방식에 따른 세 가지 패러다임입니다.

3. **손실 함수와 경사 하강법**이 학습의 핵심 메커니즘입니다.
   "얼마나 틀렸는지 측정하고, 덜 틀리는 방향으로 조금씩 조정한다."

4. **과적합은 훈련 데이터 암기, 과소적합은 학습 부족**입니다.
   편향-분산 트레이드오프로 최적점을 찾아야 합니다.

5. **데이터 분할은 공정한 평가를 위한 필수 장치**입니다.
   Train은 학습, Validation은 튜닝, Test는 최종 평가에 사용합니다.

> **다음 장 예고:** Chapter 03에서는 이러한 머신러닝 기초 위에 **딥러닝과 신경망**이
> 어떻게 구축되는지 알아봅니다. "왜 레이어를 깊게 쌓으면 더 복잡한 패턴을 학습할 수
> 있는가?"에 대한 답을 찾아보겠습니다.
