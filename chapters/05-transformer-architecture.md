# Chapter 05. Transformer 아키텍처 -- 모든 것의 시작

> "Attention is all you need." -- Vaswani et al., 2017

---

## 핵심 키워드 (Keywords)

| 키워드 | 설명 |
|--------|------|
| **Self-Attention** | 시퀀스 내 모든 위치가 서로를 참조하는 메커니즘 |
| **Multi-Head Attention** | 여러 관점에서 동시에 Attention을 수행하는 구조 |
| **Query / Key / Value** | Attention 연산의 세 가지 구성 요소 |
| **Positional Encoding** | 순서 정보를 벡터에 주입하는 방법 |
| **Feed-Forward Network** | 각 Transformer 층의 비선형 변환 네트워크 |
| **Layer Normalization** | 학습 안정화를 위한 정규화 기법 |
| **Residual Connection** | 입력을 출력에 더해 기울기 소실을 방지하는 연결 |
| **Encoder-Decoder** | 입력을 인코딩하고 출력을 디코딩하는 이중 구조 |
| **Decoder-Only** | GPT, Claude 등 생성 모델이 채택한 디코더 전용 구조 |

---

## 5.1 "Attention Is All You Need" -- 왜 혁명이었는가

2017년, Google Brain 연구팀이 발표한 논문 "Attention Is All You Need"는 인공지능의
흐름을 완전히 바꿔놓았다. 이 논문의 **Transformer** 아키텍처는 오늘날 거의 모든
대규모 언어 모델(LLM)의 기반이다.

### 논문 이전의 세계

Transformer 이전, 자연어처리(NLP)의 주류는 **RNN(Recurrent Neural Network)**과
그 변형인 **LSTM**, **GRU**였다. 이들은 시퀀스를 순차적으로 처리하는 구조 때문에
치명적 한계가 있었다.

```
RNN의 순차 처리:
  "나는" → "오늘" → "서울에" → "갔다"
    ↓         ↓         ↓         ↓
  [h1]  →  [h2]  →   [h3]  →  [h4]
  * 각 단계가 이전 결과를 기다려야 한다 (병렬화 불가)
  * 문장이 길어지면 앞쪽 정보가 희미해진다 (장기 의존성 문제)
```

**RNN의 핵심 문제점:**
1. **병렬화 불가**: 각 시점이 이전 은닉 상태에 의존 -- GPU 병렬 연산 활용 불가.
2. **장기 의존성 문제(Long-Range Dependency)**: "어제 만난 친구가 ...(200단어)...
   했다"에서 "했다"가 "친구가"를 제대로 참조하기 어렵다.
3. **기울기 소실(Vanishing Gradient)**: 역전파 시 긴 시퀀스를 거슬러 올라갈수록
   기울기가 0에 수렴한다.

### Transformer의 핵심 통찰

> **순환(Recurrence)도, 합성곱(Convolution)도 필요 없다. Attention만으로 충분하다.**

이 선언이 가져온 세 가지 변화:
1. **완전한 병렬 처리**: 시퀀스의 모든 위치를 동시에 처리할 수 있다.
2. **직접적인 장거리 연결**: 아무리 먼 위치라도 한 번의 연산으로 참조한다.
3. **확장성(Scalability)**: 모델과 데이터를 키울수록 성능이 향상되는 구조.

---

## 5.2 Self-Attention 메커니즘 -- 핵심 중의 핵심

Self-Attention은 Transformer의 심장이다. 시퀀스 내의 각 요소가 다른 모든 요소를
"참조"하여 자신의 표현(representation)을 업데이트하는 메커니즘이다.

### 5.2.1 Query, Key, Value -- 도서관 비유로 이해하기

```
도서관 검색 비유:
  당신(Query)     :  "딥러닝의 역사에 대해 알고 싶어요"
                         ↓
  사서가 확인하는  :  각 책의 제목/색인(Key)을 질문과 대조
  책 목록(Key)        → "AI 개론" (관련도: 0.7)
                      → "딥러닝 교과서" (관련도: 0.95)
                      → "요리 백과" (관련도: 0.01)
                         ↓
  실제로 가져오는  :  관련도에 비례하여 각 책의 내용(Value)을 조합
  정보(Value)         → 딥러닝 교과서 95% + AI 개론 70% + ...
```

| 구성 요소 | 도서관 비유 | 역할 |
|-----------|-------------|------|
| **Query (Q)** | 검색 질문 | "내가 알고 싶은 것" |
| **Key (K)** | 책 제목/색인 | "각 정보가 제공하는 것" |
| **Value (V)** | 책의 실제 내용 | "실제로 전달되는 정보" |

Self-Attention에서는 같은 시퀀스 내의 각 토큰이 동시에 Query이자 Key이자 Value가
된다. 모든 토큰이 다른 모든 토큰에게 "나와 얼마나 관련 있어?"라고 묻고,
그 관련도에 따라 정보를 가중 합산한다.

### 5.2.2 Q, K, V는 어떻게 만들어지는가

입력 토큰의 임베딩 벡터 **x**에 서로 다른 가중치 행렬을 곱하여 생성한다.

```
입력 벡터 x (임베딩)
     ├──── x * W_Q ────→ Query (Q)    "내가 찾는 것"
     ├──── x * W_K ────→ Key (K)      "나를 설명하는 것"
     └──── x * W_V ────→ Value (V)    "내가 전달할 정보"
* W_Q, W_K, W_V 는 학습되는 파라미터 행렬이다.
```

### 5.2.3 Attention Score 계산 공식

```
                          Q * K^T
  Attention(Q, K, V) = softmax( --------- ) * V
                          sqrt(d_k)
```

**1단계 -- 유사도 계산 (Q * K^T)**: Query와 Key의 내적으로 유사도 점수를 매긴다.

**2단계 -- 스케일링 (/ sqrt(d_k))**: Key 차원 수(d_k)의 제곱근으로 나눈다.
차원이 커지면 내적 값도 커져 softmax가 극단값으로 수렴하기 때문이다.

> 비유: 10점 만점의 8점과 10000점 만점의 8000점은 본질적으로 같다.
> 스케일링은 이 "만점"을 통일하는 작업이다.

**3단계 -- Softmax**: 점수를 0~1 사이 확률 분포로 변환. 가중치 합은 1이 된다.

**4단계 -- 가중 합산 (* V)**: 확률 분포를 Value에 곱하여 관련도에 비례한
정보를 합산한다.

### 5.2.4 구체적 수치 예제 -- 단계별 Self-Attention 계산

"나는 고양이를 좋아한다" 3개 토큰으로 직접 계산해 보자 (d_k = 3으로 단순화).

**[준비] 각 토큰의 Q, K, V 벡터 (학습된 값이라 가정)**

```
토큰         Query (Q)        Key (K)          Value (V)
--------    --------------   --------------   --------------
나는        [1.0, 0.5, 0.2]  [0.8, 0.3, 0.1]  [1.0, 0.0, 0.5]
고양이를    [0.3, 1.2, 0.8]  [0.2, 1.0, 0.7]  [0.0, 1.0, 0.3]
좋아한다    [0.7, 0.4, 1.0]  [0.6, 0.5, 0.9]  [0.5, 0.5, 1.0]
```

**[1단계] "좋아한다"의 Query로 모든 Key와 유사도 계산**

```
Q_좋아한다 = [0.7, 0.4, 1.0]
Q . K_나는     = (0.7*0.8) + (0.4*0.3) + (1.0*0.1) = 0.78
Q . K_고양이를 = (0.7*0.2) + (0.4*1.0) + (1.0*0.7) = 1.24
Q . K_좋아한다 = (0.7*0.6) + (0.4*0.5) + (1.0*0.9) = 1.52
유사도 점수: [0.78, 1.24, 1.52]
```

**[2단계] 스케일링: sqrt(3) = 1.732로 나누기**

```
스케일링 결과: [0.450, 0.716, 0.878]
```

**[3단계] Softmax 적용**

```
e^0.450=1.568, e^0.716=2.046, e^0.878=2.406  (합계=6.020)
Attention Weights: [0.260, 0.340, 0.400]
```

해석: "좋아한다"는 자기 자신(0.400)에 가장 주목하고, "고양이를"(0.340)에
그다음으로 주목한다. "좋아한다"의 대상이 "고양이를"이므로 직관적으로 타당하다.

**[4단계] 가중 합산: Attention Weight * Value**

```
출력 = 0.260 * [1.0, 0.0, 0.5]  →  [0.260, 0.000, 0.130]
     + 0.340 * [0.0, 1.0, 0.3]  →  [0.000, 0.340, 0.102]
     + 0.400 * [0.5, 0.5, 1.0]  →  [0.200, 0.200, 0.400]
                                 =  [0.460, 0.540, 0.632]
```

결과 `[0.460, 0.540, 0.632]`가 "좋아한다"의 새로운 표현이다.
"고양이를"과 "나는"의 문맥 정보가 녹아든 것이다.

```
Self-Attention 전체 흐름:
  입력 임베딩       Q, K, V 생성       Attention 계산        출력
  [나는]     ──→   Q1, K1, V1  ──┐
  [고양이를] ──→   Q2, K2, V2  ──┼──→ softmax(QK^T/√d) ──→ [z1]
  [좋아한다] ──→   Q3, K3, V3  ──┘       * V              [z2]
                                                           [z3]
```

---

## 5.3 Multi-Head Attention -- 여러 관점에서 동시에 보기

하나의 Attention으로는 한 가지 관계만 포착할 수 있다. 하지만 자연어에는 여러
층위의 관계가 동시에 존재한다. "The animal didn't cross the street because
it was too tired"에서 "it"을 이해하려면 문법적("it"의 선행사), 의미적("tired"는
생물의 속성), 구조적(주어 위치) 관계를 동시에 파악해야 한다.

Multi-Head Attention은 **여러 독립적인 Attention Head**로 이를 해결한다.

```
Multi-Head Attention:
  입력 (X)
    ├───→ [Head 1] Q1,K1,V1 → Attention → z1  (문법적 관계)
    ├───→ [Head 2] Q2,K2,V2 → Attention → z2  (의미적 관계)
    ├───→ [Head 3] Q3,K3,V3 → Attention → z3  (위치 관계)
    └───→ [Head h] Qh,Kh,Vh → Attention → zh
                         │
         Concat(z1, ..., zh) ──→ W_O ──→ 최종 출력

수학적으로:
  MultiHead(Q, K, V) = Concat(head_1, ..., head_h) * W_O
  head_i = Attention(X * W_Q_i, X * W_K_i, X * W_V_i)
```

**핵심**: 각 Head는 독립적인 W_Q, W_K, W_V를 가져 서로 다른 관계를 학습한다.
전체 차원 d_model을 h개로 나누므로(d_model=512, h=8이면 d_k=64) 계산 비용은
단일 Attention과 거의 동일하면서 표현력은 훨씬 강하다.

```
모델               d_model    Head 수    d_k (= d_model/h)
────────────────   ──────     ──────     ─────────────────
Transformer 원본      512          8                  64
GPT-2 Small           768         12                  64
GPT-3 (175B)        12288         96                 128
```

---

## 5.4 Positional Encoding -- 순서 정보를 주입하는 방법

RNN은 토큰을 순차 처리하므로 순서가 자연스럽게 내장된다. 하지만 Transformer의
Self-Attention은 **모든 위치를 동시에, 동등하게** 처리한다. "나는 고양이를
좋아한다"와 "고양이를 나는 좋아한다"를 구분하지 못하는 것이다.

### Sinusoidal Positional Encoding

```
PE(pos, 2i)   = sin(pos / 10000^(2i/d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d_model))
  pos = 토큰 위치,  i = 차원 인덱스,  d_model = 임베딩 차원 수
```

**왜 사인/코사인인가?**
1. **주기성**: 낮은 차원은 빠르게 진동하고(가까운 위치 구분), 높은 차원은 느리게
   진동한다(먼 위치 구분). 위치를 다중 스케일로 표현한다.
2. **상대적 위치 표현**: sin/cos의 수학적 성질 덕분에 두 위치 간의 상대적 거리를
   선형 변환으로 표현할 수 있다.
3. **무한 확장성**: 학습 시 보지 못한 길이에도 인코딩을 생성할 수 있다.

```
직관적 이미지:
  차원 0 (고주파):  ⌇⌇⌇⌇⌇⌇⌇⌇⌇⌇  → 인접 위치를 세밀하게 구분
  차원 1:          ∿∿∿∿∿∿∿∿∿∿    → 중간 거리를 구분
  차원 2 (저주파):  ___---___---   → 먼 거리를 구분
  위치:  0  1  2  3  4  5  6  7  8  9  ...
```

이 인코딩은 **입력 임베딩에 더해진다**: `최종 입력 = Token Embedding + Positional Encoding`

> **참고**: 최근 모델들(GPT, LLaMA)은 RoPE(Rotary Position Embedding) 같은 발전된
> 방법을 사용하지만, 핵심 아이디어 -- 위치를 벡터로 인코딩 -- 는 동일하다.

---

## 5.5 Feed-Forward Network (FFN)

각 Transformer 층에는 Attention 이후 **위치별 FFN(Position-wise FFN)**이 있다.

```
FFN(x) = ReLU(x * W_1 + b_1) * W_2 + b_2

  입력 (d_model=512) → [Linear: 512→2048] → [ReLU] → [Linear: 2048→512] → 출력
                         확장                          압축
```

**왜 필요한가?** Self-Attention은 본질적으로 가중 평균 연산이라 비선형 변환이
불가능하다. FFN이 비선형 변환을 담당하여 표현력을 높인다. 최근 연구에 따르면
FFN은 "지식 저장소" 역할을 하며, 학습 중 습득한 사실적 지식이 여기 저장된다.

FFN은 각 토큰에 독립적으로 적용된다("Position-wise"). 모든 토큰이 같은 가중치를
공유하되, 각자 독립적으로 변환된다.

---

## 5.6 Layer Normalization과 Residual Connection

### Residual Connection (잔차 연결)

```
  입력 (x) ───────────────────┐
       ▼                      │ (skip connection)
  [Sub-Layer: Attention/FFN]  │
       ▼                      ▼
    출력 ────────(+)───────── x  →  x + SubLayer(x)
```

**효과**: (1) 기울기가 Skip Connection을 통해 직접 전달 -- 기울기 소실 방지,
(2) 전체 변환 대신 "변화량"만 학습 -- 학습 용이, (3) 입력 정보 직접 전달 -- 보존.

### Layer Normalization (층 정규화)

```
LayerNorm(x) = gamma * (x - mean) / sqrt(variance + epsilon) + beta
  mean, variance: 토큰 벡터 내 모든 차원에 대해 계산
  gamma, beta: 학습 파라미터
```

NLP에서는 시퀀스 길이가 가변적이므로 Batch Norm 적용이 어렵다. Layer Norm은
배치 크기에 무관하게 작동하며, 추론 시에도 학습 시와 동일하게 동작한다.

---

## 5.7 Transformer 블록의 전체 구조

```
╔══════════════════════════════════════════════════════╗
║               Transformer Block (1개 층)              ║
╠══════════════════════════════════════════════════════╣
║  입력 (x)                                            ║
║    ├───────────────────────────┐                     ║
║    ▼                           │                     ║
║  ┌─────────────────────┐       │                     ║
║  │ Multi-Head Attention │       │                     ║
║  └──────────┬──────────┘       │                     ║
║             ▼                  │                     ║
║          Add (+ x) ◄──────────┘  ← Residual         ║
║             ▼                                        ║
║        Layer Norm                                    ║
║             ├───────────────────┐                    ║
║             ▼                   │                    ║
║  ┌─────────────────────┐        │                    ║
║  │ Feed-Forward Network │        │                    ║
║  └──────────┬──────────┘        │                    ║
║             ▼                   │                    ║
║          Add (+ x) ◄───────────┘  ← Residual        ║
║             ▼                                        ║
║        Layer Norm                                    ║
║             ▼                                        ║
║          출력 (다음 블록의 입력)                       ║
╚══════════════════════════════════════════════════════╝

한 줄 요약: x → [Attention] → [Add & Norm] → [FFN] → [Add & Norm] → 출력
이 블록을 N번 적층하면 Transformer 완성 (원본 논문: N=6).
```

> **Pre-Norm vs Post-Norm**: 원본은 Post-Norm이지만, GPT-2부터 Pre-Norm(Sub-Layer
> 앞에 Norm)이 표준이 되었다. 학습 안정성이 더 좋기 때문이다.

---

## 5.8 Encoder-Decoder, Decoder-Only, Encoder-Only

### 원본 Transformer: Encoder-Decoder 구조

```
┌────────────────────┐     ┌────────────────────────┐
│     ENCODER        │     │      DECODER           │
│                    │     │                        │
│  [입력 시퀀스]     │     │  [출력 시퀀스]          │
│       ▼            │     │       ▼                │
│  Self-Attention    │     │  Masked Self-Attention  │
│  (양방향)          │     │  (단방향: 왼쪽만)       │
│       ▼            │     │       ▼                │
│  Add & Norm        │     │  Add & Norm            │
│       ▼            │     │       ▼                │
│  FFN → Add & Norm  │     │  Cross-Attention ◄─────┼── Encoder 출력
│       ▼            │     │  Add & Norm            │
│  [Encoder 출력] ───┼──→  │       ▼                │
│   x N layers       │     │  FFN → Add & Norm      │
└────────────────────┘     │       ▼                │
                           │  [다음 토큰 예측]       │
                           │   x N layers           │
                           └────────────────────────┘
```

- **Encoder**: 양방향 Self-Attention으로 입력 전체를 이해한다.
- **Decoder**: Masked Self-Attention(미래 차단) + Cross-Attention(Encoder 참조).
- **대표 모델**: T5, BART / **용도**: 번역, 요약

### Decoder-Only 구조 (GPT 스타일) -- 현재 LLM의 주류

```
┌───────────────────────────────┐
│        DECODER-ONLY           │
│  [입력+출력을 하나의 시퀀스]   │
│         ▼                     │
│  Masked Self-Attention        │
│  (이전 토큰만 참조)            │
│         ▼                     │
│  Add & Norm → FFN → Add & Norm│
│         ▼                     │
│  [다음 토큰 예측]  x N layers │
└───────────────────────────────┘

Causal Mask:
        나는  오늘  서울에  갔다
나는     O     X     X      X    ← 자기만 참조
오늘     O     O     X      X
서울에   O     O     O      X
갔다     O     O     O      O    ← 전부 참조 가능
```

- Encoder 없이 입력과 출력을 하나의 시퀀스로 처리한다.
- **대표 모델**: GPT, Claude, LLaMA, PaLM
- **왜 주류인가**: Scaling Law에 유리하고 하나의 모델로 다양한 작업 수행 가능.

### Encoder-Only 구조 (BERT 스타일)

양방향 Attention으로 입력 전체를 이해. "생성"이 아닌 "이해"에 특화.
**대표 모델**: BERT, RoBERTa, DeBERTa / **용도**: 분류, NER, 유사도

### 비교 요약

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│              │ Enc-Dec      │ Dec-Only     │ Enc-Only     │
├──────────────┼──────────────┼──────────────┼──────────────┤
│ Attention    │ 양방향+단방향│ 단방향(왼→오)│ 양방향       │
│ 대표 모델    │ T5, BART     │ GPT, Claude  │ BERT         │
│ 주요 용도    │ 번역, 요약   │ 생성, 대화   │ 분류, 이해   │
│ 현재 위상    │ 특정 작업    │ ★ LLM 표준   │ NLU 특화     │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

---

## 5.9 왜 Transformer가 RNN을 대체했는가 -- 병렬처리의 힘

```
RNN:          t=1 → t=2 → t=3 → t=4 → ...  (순차, GPU 코어 1개)
Transformer:  t=1, t=2, t=3, t=4 동시 처리   (병렬, GPU 코어 수천 개)
```

| 비교 항목 | RNN / LSTM | Transformer |
|-----------|------------|-------------|
| 시퀀스 처리 | 순차적 O(n) 스텝 | 병렬 O(1) 스텝 |
| 장거리 의존성 | 경로 길이 O(n) | 경로 길이 O(1) |
| GPU 활용 | 비효율적 | 최적화 가능 |
| 확장성 | 모델 키우기 어려움 | 수천억 파라미터까지 확장 |

**경로 길이(Path Length)**: 두 토큰이 정보를 교환하기 위해 거치는 연산 단계 수.

```
100개 토큰에서 첫 번째 ↔ 마지막:
  RNN:         [토큰1] → ... → [토큰100]  경로 = 99
  Transformer: [토큰1] ← Attention → [토큰100]  경로 = 1
```

경로가 짧을수록 기울기 전달이 잘 되고, 정보 손실이 적고, 장거리 의존성을
잘 포착한다. 이 병렬성 덕분에 GPT-3(1750억), PaLM(5400억) 등 초거대 모델이
가능해졌다. RNN으로는 물리적으로 불가능한 규모다.

---

## 5.10 계산 복잡도 -- O(n^2) Attention과 그 함의

핵심 연산 Q * K^T에서 시퀀스 길이 n, 벡터 차원 d일 때:

```
시간 복잡도: O(n^2 * d)  ← 모든 토큰 쌍에 대해 내적
메모리 복잡도: O(n^2)    ← n x n Attention 행렬 저장
```

**실제적 의미** -- 길이를 2배 늘리면 비용이 4배 증가:

```
시퀀스 길이(n)    Attention 행렬     상대적 비용
512               262K                    1x
4,096             16M                    64x
32,768            1B                  4,096x
131,072 (128K)    17B                65,536x
```

이것이 초기 모델의 컨텍스트 윈도우가 512~2048 토큰으로 제한된 이유다.

### 해결을 위한 노력들

| 방법 | 핵심 아이디어 | 복잡도 |
|------|-------------|--------|
| **Sparse Attention** | 일부 쌍만 계산 | O(n * sqrt(n)) |
| **Linear Attention** | Kernel 근사 | O(n * d) |
| **Flash Attention** | GPU 메모리 계층 최적화 | 이론 동일, 실제 2-4배 빠름 |
| **Sliding Window** | 로컬 윈도우만 Attention | O(n * w) |

> **Flash Attention**(2022, Tri Dao): GPU의 SRAM/HBM 메모리 계층을 최적화하여
> 실제 속도를 2~4배 향상. 현재 거의 모든 주요 LLM에서 사용된다.

---

## 5.11 전체 Transformer 아키텍처 종합도

```
               Transformer 전체 아키텍처 (Encoder-Decoder)

 입력 ("I love cats")                      출력 ("<start> 나는 고양이를")
      │                                         │
      ▼                                         ▼
 ┌──────────┐                              ┌──────────┐
 │  Input    │                              │  Output   │
 │ Embedding │                              │ Embedding │
 │ + Pos Enc │                              │ + Pos Enc │
 └────┬─────┘                              └────┬─────┘
      ▼                                         ▼
 ┌─────────────┐                          ┌──────────────────┐
 │ Self-Attn   │                          │ Masked Self-Attn │
 │ Add & Norm  │                          │ Add & Norm       │
 │             │                          │                  │
 │ FFN         │                          │ Cross-Attention  │
 │ Add & Norm  │── Encoder 출력 ─────────→│ Add & Norm       │
 │             │                          │                  │
 │  x N층      │                          │ FFN              │
 └─────────────┘                          │ Add & Norm       │
                                          │  x N층           │
                                          └────────┬─────────┘
                                                   ▼
                                            ┌────────────┐
                                            │  Linear +   │
                                            │  Softmax    │
                                            └──────┬─────┘
                                                   ▼
                                            다음 토큰 확률 분포
```

---

## 5.12 정리 -- Transformer가 바꾼 세상

**1. Self-Attention**: 모든 위치가 서로를 직접 참조. QKV로 주목 대상을 학습.

**2. Multi-Head Attention**: 문법/의미/구조 등 다양한 관계를 병렬로 포착.

**3. Positional Encoding**: 삼각함수로 순서 정보 주입. 순환 없이 순서를 이해.

**4. FFN + Residual + LayerNorm**: 비선형 변환, 기울기 안정화, 정보 보존.

**5. 세 가지 변형**: Enc-Dec(번역), Dec-Only(생성/LLM 주류), Enc-Only(이해).

**6. 병렬화와 확장성**: RNN의 순차 처리를 극복, 수천억 파라미터 규모 학습 가능.

2017년 이후, GPT, BERT, T5, LLaMA, Claude, Gemini -- 거의 모든 주요 AI 모델이
Transformer를 기반으로 만들어졌다. 당신이 매일 사용하는 Claude Code, GitHub
Copilot, 그리고 이 글을 쓰고 있는 AI의 내부에도 이 Transformer가 작동하고 있다.

> 다음 Chapter 06에서는 Transformer 위에 어떻게 LLM이 구축되는지,
> 파라미터 스케일링과 창발적 능력에 대해 깊이 다룬다.

---

## 참고 문헌

1. Vaswani, A., et al. (2017). *Attention Is All You Need*. NeurIPS 2017.
2. He, K., et al. (2015). *Deep Residual Learning for Image Recognition*. CVPR 2016.
3. Ba, J. L., et al. (2016). *Layer Normalization*. arXiv:1607.06450.
4. Dao, T., et al. (2022). *FlashAttention*. NeurIPS 2022.
5. Devlin, J., et al. (2018). *BERT*. NAACL 2019.
6. Radford, A., et al. (2018). *Improving Language Understanding by Generative Pre-Training*.
