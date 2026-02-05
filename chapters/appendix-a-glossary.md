# Appendix A: 용어 사전 (Glossary)

> **이 용어 사전은 본 가이드 전체에서 등장하는 AI/ML/LLM 관련 핵심 용어를 A-Z 순서로 정리한 것입니다.**
> 각 용어에는 영문 표기, 한국어 번역(해당 시), 간결한 정의, 그리고 관련 챕터 번호가 포함되어 있습니다.

---

## A

### Activation Function (활성화 함수)
> 신경망의 각 뉴런 출력에 비선형성을 부여하는 함수이다. ReLU, Sigmoid, Tanh 등이 대표적이며, 활성화 함수가 없으면 아무리 층을 깊게 쌓아도 하나의 선형 변환에 불과하다. 비선형 활성화 함수가 있어야 신경망이 복잡한 패턴을 학습할 수 있다. → Chapter 03

### AGI (Artificial General Intelligence, 인공 일반 지능)
> 특정 작업이 아닌 인간 수준의 범용적 지적 능력을 갖춘 AI를 의미한다. 현재의 AI는 특정 작업에 특화된 좁은 AI(Narrow AI)이며, AGI는 아직 실현되지 않은 목표로서 활발한 논의가 진행 중이다. → Chapter 19

### Agent Loop (에이전트 루프)
> AI 에이전트가 작업을 수행하는 반복 순환 구조로, "관찰(Observe) → 사고(Think) → 행동(Act) → 반복"의 패턴을 따른다. 에이전트는 이 루프를 통해 환경과 상호작용하면서 목표를 달성한다. → Chapter 11

### Agentic AI (에이전틱 AI)
> 인간의 지시를 받아 스스로 계획을 세우고, 도구를 활용하며, 자율적으로 작업을 수행하는 AI 시스템을 의미한다. 단순한 질의응답을 넘어 복잡한 작업을 독립적으로 처리할 수 있는 AI의 발전 방향이다. → Chapter 11, Chapter 19

### AI Safety (AI 안전성)
> AI 시스템이 인간에게 해를 끼치지 않고 의도한 대로 동작하도록 보장하는 연구 분야이다. 편향, 오용, 제어 불가능성 등의 위험을 최소화하는 기술적, 정책적 접근을 포함한다. → Chapter 19

### AI Winter (AI 겨울)
> AI에 대한 과도한 기대 이후 투자와 관심이 급감한 침체기를 말한다. 역사적으로 1970년대와 1990년대에 두 차례 발생했으며, 하이프 사이클의 전형적 패턴을 보여준다. → Chapter 01

### Alignment (정렬)
> AI 시스템의 목표와 행동을 인간의 가치관 및 의도에 맞추는 것을 의미한다. 모델이 유용하면서도 안전하고 정직하게 동작하도록 하는 기술적 과제이며, RLHF, Constitutional AI 등이 대표적인 정렬 기법이다. → Chapter 07, Chapter 19

### API (Application Programming Interface)
> 소프트웨어 간의 상호작용을 위한 인터페이스 규약이다. LLM 분야에서는 모델에 프롬프트를 보내고 응답을 받는 HTTP 기반 인터페이스를 주로 가리키며, 토큰 수 기반으로 과금되는 것이 일반적이다. → Chapter 14

### ASI (Artificial Superintelligence, 인공 초지능)
> 모든 분야에서 인간의 지적 능력을 초월하는 AI를 의미한다. AGI를 넘어서는 개념으로, 실현 가능성과 시기에 대해 전문가들 사이에서 의견이 크게 엇갈리고 있다. → Chapter 19

### Attention (어텐션)
> 입력 시퀀스의 각 부분에 서로 다른 가중치를 부여하여, 현재 처리 중인 요소와 관련성이 높은 부분에 더 집중하는 메커니즘이다. 2014년 Bahdanau 등이 제안했으며, Seq2Seq 모델의 정보 병목 문제를 해결하고 Transformer의 직접적 기반이 되었다. → Chapter 04, Chapter 05

### Autoregressive (자기회귀)
> 이전에 생성한 출력을 다음 입력으로 사용하여 순차적으로 토큰을 생성하는 방식이다. GPT, Claude 등 대부분의 LLM은 자기회귀 방식으로 다음 토큰을 하나씩 예측하며 텍스트를 생성한다. → Chapter 06, Chapter 08

---

## B

### Backpropagation (역전파)
> 신경망의 출력 오차를 역방향으로 전파하여 각 가중치의 기여도(기울기)를 계산하는 학습 알고리즘이다. 미적분의 연쇄 법칙(Chain Rule)에 기반하며, 현대 딥러닝의 핵심 학습 메커니즘이다. → Chapter 03

### Bag of Words (BoW)
> 텍스트에서 단어의 출현 빈도만으로 문서를 벡터로 표현하는 전통적 방법이다. 단어의 순서 정보를 완전히 무시하며, 대부분이 0인 희소 벡터(sparse vector)가 되는 한계가 있다. → Chapter 04

### Batch Normalization (배치 정규화)
> 신경망의 각 층에서 미니배치 단위로 입력 분포를 정규화하여 학습을 안정화하는 기법이다. 내부 공변량 이동(Internal Covariate Shift) 문제를 완화하고, 더 큰 학습률 사용과 빠른 수렴을 가능하게 한다. → Chapter 03

### Batch Size (배치 크기)
> 한 번의 가중치 갱신에 사용되는 훈련 데이터 샘플의 수이다. 배치 크기가 크면 학습이 안정적이지만 메모리 사용량이 증가하고, 작으면 노이즈가 많지만 지역 최솟값을 탈출하는 데 도움이 될 수 있다. → Chapter 02, Chapter 03

### Beam Search (빔 서치)
> 텍스트 생성 시 매 단계에서 상위 k개의 후보를 유지하며 탐색하는 디코딩 전략이다. Greedy Decoding보다 더 나은 전체 시퀀스를 찾을 수 있지만, 다양성이 떨어지고 계산 비용이 높아진다. → Chapter 08

### Benchmark (벤치마크)
> AI 모델의 성능을 표준화된 방식으로 평가하기 위한 테스트 세트와 평가 지표의 조합이다. MMLU, HumanEval, GPQA 등이 대표적인 LLM 벤치마크이며, 모델 간 객관적 비교를 가능하게 한다. → Chapter 16

### BERT (Bidirectional Encoder Representations from Transformers)
> 2018년 Google이 발표한 양방향 Transformer Encoder 기반의 언어 모델이다. 문맥의 앞뒤를 동시에 참조하여 단어를 이해하며, 마스크된 토큰을 예측하는 방식(MLM)으로 사전학습한다. 분류, 질의응답 등 NLU(자연어 이해) 작업에 강점을 보인다. → Chapter 06

### Bias (편향)
> 머신러닝에서 두 가지 의미로 쓰인다. (1) 모델이 너무 단순한 가정을 함으로써 발생하는 체계적 오차(높은 편향 = 과소적합). (2) 학습 데이터에 내재된 사회적 편향이 모델에 반영되는 문제. 두 경우 모두 모델 성능과 공정성에 부정적 영향을 미친다. → Chapter 02

### BPE (Byte Pair Encoding, 바이트 페어 인코딩)
> 가장 빈번하게 함께 등장하는 문자 쌍을 반복적으로 병합하여 서브워드 어휘를 구축하는 토큰화 알고리즘이다. GPT, Claude, LLaMA 등 대부분의 현대 LLM이 채택하고 있으며, 미등록어(OOV) 문제를 효과적으로 해결한다. → Chapter 04

---

## C

### Chain-of-Thought (CoT, 사고의 사슬)
> LLM에게 중간 추론 단계를 명시적으로 보여주도록 유도하는 프롬프팅 기법이다. "단계별로 생각해봐"와 같은 지시를 통해 복잡한 추론 문제에서 정확도를 크게 향상시킬 수 있다. → Chapter 09, Chapter 18

### Chunking (청킹)
> RAG 파이프라인에서 긴 문서를 검색에 적합한 작은 단위(청크)로 분할하는 과정이다. 청크 크기, 오버랩 비율, 분할 기준(문단, 문장, 토큰 수) 등의 전략이 검색 품질에 큰 영향을 미친다. → Chapter 10

### CNN (Convolutional Neural Network, 합성곱 신경망)
> 합성곱 연산을 통해 입력 데이터(주로 이미지)의 공간적 특징을 자동으로 추출하는 신경망 구조이다. 필터(커널)를 슬라이딩하며 지역적 패턴을 감지하고, 풀링으로 공간을 축소한다. ImageNet 대회에서의 AlexNet 성공이 딥러닝 혁명을 촉발했다. → Chapter 03

### Constitutional AI (헌법적 AI)
> Anthropic이 제안한 AI 안전성 접근법으로, AI의 행동을 미리 정의된 원칙(헌법)에 따라 자기 평가하고 수정하게 하는 방식이다. 인간 피드백에 대한 의존도를 줄이면서도 안전하고 유용한 모델을 만드는 것을 목표로 한다. Claude가 이 방식으로 학습되었다. → Chapter 07

### Context Window (컨텍스트 윈도우)
> LLM이 한 번에 처리할 수 있는 최대 토큰 수를 의미한다. 입력 프롬프트와 생성 출력을 합친 전체 길이가 이 범위 내에 있어야 한다. 모델에 따라 4K에서 200K+ 토큰까지 다양하며, 긴 컨텍스트를 지원할수록 더 많은 정보를 한 번에 처리할 수 있다. → Chapter 06

### Cross-Validation (교차 검증)
> 데이터를 여러 번 다르게 분할하여 모델을 반복 평가하는 기법이다. K-Fold 교차 검증에서는 데이터를 K개 구간으로 나누어 각 구간이 한 번씩 검증 세트가 된다. 데이터가 적을 때 더 안정적인 성능 추정이 가능하다. → Chapter 02

### CUDA (Compute Unified Device Architecture)
> NVIDIA가 개발한 GPU 병렬 컴퓨팅 플랫폼 및 프로그래밍 모델이다. 딥러닝의 핵심인 행렬 연산을 GPU에서 대규모 병렬로 처리할 수 있게 해주며, PyTorch, TensorFlow 등 대부분의 딥러닝 프레임워크가 CUDA를 기반으로 동작한다. → Chapter 14

---

## D

### Data Curation (데이터 큐레이션)
> LLM 학습에 사용할 데이터를 수집, 정제, 필터링, 품질 관리하는 전 과정을 말한다. 학습 데이터의 품질이 모델 성능을 결정하는 가장 중요한 요소 중 하나이며, 중복 제거, 유해 콘텐츠 필터링, 다양성 확보 등이 포함된다. → Chapter 07

### Decoder (디코더)
> Encoder-Decoder 구조에서 인코더가 만든 표현을 받아 출력 시퀀스를 생성하는 부분이다. GPT, Claude 등의 모델은 Decoder-Only 구조로, 입력 토큰을 처리하고 다음 토큰을 순차적으로 예측한다. → Chapter 04, Chapter 05

### Diffusion Model (확산 모델)
> 데이터에 점진적으로 노이즈를 추가하는 과정을 역으로 학습하여 새로운 데이터를 생성하는 모델이다. DALL-E, Stable Diffusion, Midjourney 등 현대 이미지 생성 AI의 핵심 아키텍처로, 고품질 이미지 생성에 탁월한 성능을 보인다. → Chapter 17

### DPO (Direct Preference Optimization, 직접 선호 최적화)
> RLHF의 복잡한 보상 모델 학습 단계를 생략하고, 인간의 선호 데이터로부터 직접 정책을 최적화하는 학습 기법이다. RLHF보다 구현이 간단하고 학습이 안정적이어서, 최근 많은 모델 학습에 채택되고 있다. → Chapter 07

### Dropout (드롭아웃)
> 학습 과정에서 무작위로 일부 뉴런을 비활성화하여 과적합을 방지하는 정규화 기법이다. 매번 다른 부분 네트워크로 학습하는 효과가 있어, 앙상블과 유사한 일반화 성능 향상을 얻을 수 있다. 추론 시에는 모든 뉴런을 사용한다. → Chapter 03

---

## E

### Embedding (임베딩)
> 단어, 문장, 문서 등을 고정 길이의 실수 벡터로 변환하는 기법 또는 그 결과물이다. 의미가 유사한 항목은 벡터 공간에서 가까운 위치에 배치된다. LLM의 첫 번째 층에서 토큰을 벡터로 변환하는 과정이자, RAG에서 유사도 검색의 핵심 원리이다. → Chapter 04, Chapter 10

### Embedding Search (임베딩 검색)
> 텍스트를 임베딩 벡터로 변환한 뒤, 벡터 간 유사도(코사인 유사도 등)를 기반으로 의미적으로 유사한 문서를 검색하는 방법이다. 키워드가 정확히 일치하지 않아도 의미가 유사하면 검색이 가능하다는 장점이 있다. → Chapter 10

### Emergent Ability (창발적 능력)
> 모델의 규모가 일정 임계점을 넘었을 때 갑자기 나타나는 새로운 능력을 의미한다. 소규모 모델에서는 보이지 않던 퓨샷 학습, 복잡한 추론, 코드 생성 등의 능력이 모델이 충분히 커진 뒤에야 등장하는 현상이다. → Chapter 06

### Encoder (인코더)
> 입력 시퀀스를 읽어 내부 표현(벡터)으로 압축하는 신경망 구성요소이다. Encoder-Decoder 구조에서 입력을 이해하는 역할을 담당하며, BERT는 Encoder-Only 구조의 대표적 모델이다. → Chapter 04, Chapter 05

### Epoch (에폭)
> 전체 훈련 데이터셋을 한 번 완전히 순회하는 학습 단위이다. 1 에폭은 모든 데이터를 한 번씩 본 것이며, 일반적으로 여러 에폭에 걸쳐 반복 학습하여 모델 성능을 향상시킨다. → Chapter 02, Chapter 03

### Extended Thinking (확장된 사고)
> 추론 시 모델이 더 많은 계산 자원을 사용하여 깊이 있는 사고 과정을 거치게 하는 기법이다. Claude의 Extended Thinking이나 OpenAI의 o1/o3 시리즈가 대표적이며, 복잡한 추론 문제에서 성능이 크게 향상된다. → Chapter 18

---

## F

### Feature (특성)
> 머신러닝 모델에 입력되는 개별 측정값이나 속성을 의미한다. 데이터베이스의 컬럼이나 API의 요청 파라미터에 해당하며, 좋은 특성을 설계하는 특성 공학(Feature Engineering)이 모델 성능에 결정적 영향을 미친다. → Chapter 02

### Few-shot (퓨샷)
> 모델에 소수의 입출력 예시를 프롬프트에 포함하여 원하는 작업을 수행하게 하는 기법이다. 별도의 학습 없이 프롬프트만으로 새로운 작업에 적응할 수 있으며, GPT-3에서 이 능력이 주목받기 시작했다. → Chapter 09

### Fine-tuning (미세조정)
> 사전학습된 모델을 특정 작업이나 도메인에 맞게 추가로 학습시키는 과정이다. 전체 모델 가중치를 조정하는 풀 파인튜닝과, 일부만 조정하는 효율적 방법(LoRA 등)이 있다. → Chapter 07

### Function Calling (함수 호출)
> LLM이 외부 함수나 API를 직접 호출할 수 있도록 하는 기능이다. 모델이 사용자 요청을 분석하여 적절한 함수와 인자를 결정하고, 실행 결과를 활용하여 응답을 생성한다. AI 에이전트의 핵심 구성 요소이다. → Chapter 11

---

## G

### GloVe (Global Vectors for Word Representation)
> Stanford에서 2014년에 발표한 단어 임베딩 모델로, 전체 말뭉치의 동시출현 통계(co-occurrence matrix)를 활용하여 단어 벡터를 학습한다. Word2Vec이 로컬 문맥만 보는 것과 달리, 전역 통계를 반영하여 안정적인 임베딩을 생성한다. → Chapter 04

### GPT (Generative Pre-trained Transformer)
> OpenAI가 개발한 자기회귀 방식의 대규모 언어 모델 시리즈이다. Transformer의 Decoder 구조를 사용하며, 대규모 텍스트 데이터로 사전학습한 뒤 다양한 작업에 활용한다. GPT-3(2020), GPT-4(2023) 등으로 발전하며 생성형 AI 시대를 열었다. → Chapter 06

### GPU (Graphics Processing Unit, 그래픽 처리 장치)
> 원래 그래픽 렌더링을 위해 설계된 프로세서이지만, 대규모 행렬 연산의 병렬 처리에 특화되어 딥러닝 학습과 추론의 핵심 하드웨어가 되었다. NVIDIA의 A100, H100 등이 AI 학습에 널리 사용된다. → Chapter 14

### Gradient (경사/기울기)
> 손실 함수를 모델 파라미터에 대해 미분한 값으로, 파라미터를 어느 방향으로 얼마나 조정해야 손실이 줄어드는지를 나타낸다. 역전파로 계산되며, 경사 하강법의 핵심 요소이다. → Chapter 02, Chapter 03

### Gradient Descent (경사 하강법)
> 손실 함수의 기울기(gradient)를 따라 파라미터를 최솟값 방향으로 반복 갱신하는 최적화 알고리즘이다. SGD(확률적 경사 하강법), Adam 등의 변형이 있으며, 거의 모든 신경망 학습의 기본 원리이다. → Chapter 02, Chapter 03

### Greedy Decoding (탐욕적 디코딩)
> 텍스트 생성 시 매 단계에서 확률이 가장 높은 토큰을 선택하는 가장 단순한 디코딩 전략이다. 계산 비용이 낮지만, 전체적으로 최적이 아닌 시퀀스를 생성할 수 있고 반복적인 출력이 나오기 쉽다. → Chapter 08

### GRU (Gated Recurrent Unit, 게이트 순환 유닛)
> LSTM을 단순화한 순환 신경망 구조로, Reset Gate와 Update Gate 2개만 사용한다. 셀 상태와 은닉 상태를 통합하여 LSTM보다 파라미터가 적고 빠르지만, 많은 경우 비슷한 성능을 보인다. → Chapter 03

### Guardrails (가드레일)
> LLM 기반 시스템의 출력 품질과 안전성을 보장하기 위한 보호 장치이다. 유해 콘텐츠 필터링, 출력 형식 검증, 주제 이탈 방지 등의 규칙을 적용하여 모델이 의도된 범위 내에서 동작하도록 한다. → Chapter 16

---

## H

### Hallucination (환각)
> LLM이 사실이 아닌 정보를 자신감 있게 생성하는 현상이다. 모델이 학습 데이터에 없는 내용을 그럴듯하게 지어내거나, 사실을 왜곡하는 것을 포함한다. LLM의 가장 큰 한계 중 하나이며, RAG가 이를 완화하는 대표적 방법이다. → Chapter 10

### Human-in-the-Loop (휴먼 인 더 루프)
> AI 시스템의 작업 과정에 사람의 검증, 판단, 수정을 포함하는 방식이다. AI가 초안을 생성하고 인간이 검토하는 워크플로우를 의미하며, AI의 오류를 방지하고 품질을 보장하기 위해 필수적인 접근법이다. → Chapter 15

### Hybrid Search (하이브리드 검색)
> 키워드 기반 검색(BM25 등)과 벡터 기반 의미 검색(Embedding Search)을 결합한 검색 방식이다. 정확한 키워드 매칭과 의미적 유사도 검색의 장점을 동시에 활용하여, 단일 방식보다 높은 검색 품질을 달성한다. → Chapter 10

---

## I

### Inference (추론)
> 학습이 완료된 모델이 새로운 입력에 대해 예측이나 출력을 생성하는 과정이다. LLM에서는 프롬프트를 받아 토큰을 생성하는 전체 과정을 의미하며, Temperature, Top-k, Top-p 등의 매개변수로 생성 방식을 제어한다. → Chapter 08

---

## K

### KV Cache (키-값 캐시)
> Transformer 기반 LLM의 추론 속도를 높이기 위해, 이전에 계산한 Attention의 Key와 Value 벡터를 캐싱하는 기법이다. 새로운 토큰을 생성할 때 이전 토큰들의 K, V를 재계산할 필요 없이 캐시에서 가져와 사용하므로, 추론 속도가 크게 향상된다. → Chapter 08

---

## L

### Label (레이블)
> 지도학습에서 모델이 예측해야 하는 정답 값이다. 분류 문제에서는 범주(스팸/정상), 회귀 문제에서는 연속적 수치(가격, 온도)가 레이블에 해당한다. 데이터베이스의 타겟 컬럼에 비유할 수 있다. → Chapter 02

### Learning Rate (학습률)
> 경사 하강법에서 한 번의 가중치 갱신 시 이동하는 크기를 결정하는 하이퍼파라미터이다. 너무 크면 최적점을 지나쳐 발산하고, 너무 작으면 수렴이 매우 느려진다. 적절한 학습률 설정은 모델 학습의 핵심 과제 중 하나이다. → Chapter 02, Chapter 03

### LLM (Large Language Model, 대규모 언어 모델)
> 수십억에서 수조 개의 파라미터를 가진 대규모 신경망 언어 모델이다. 방대한 텍스트 데이터로 사전학습되어 다음 토큰 예측 능력을 갖추며, 번역, 요약, 코드 생성, 대화 등 다양한 자연어 작업을 수행할 수 있다. GPT, Claude, Gemini, LLaMA 등이 대표적이다. → Chapter 06

### LoRA (Low-Rank Adaptation, 저순위 적응)
> 모델 전체 가중치를 수정하지 않고, 저순위 행렬 분해를 통해 소수의 파라미터만 추가하여 미세조정하는 효율적 학습 기법이다. 메모리와 계산 비용을 대폭 줄이면서도 풀 파인튜닝에 근접한 성능을 달성할 수 있다. → Chapter 07

### Loss Function (손실 함수)
> 모델의 예측값과 실제 정답 사이의 차이를 수치화하는 함수이다. MSE(평균 제곱 오차), Cross-Entropy 등이 대표적이며, 모델 학습은 이 손실 함수의 값을 최소화하는 방향으로 파라미터를 조정하는 과정이다. → Chapter 02

### LSTM (Long Short-Term Memory, 장단기 기억)
> Forget Gate, Input Gate, Output Gate 세 개의 게이트 메커니즘을 통해 장기 의존성 문제를 해결한 RNN의 발전형이다. 별도의 셀 상태(Cell State)가 장기 기억의 고속도로 역할을 하며, 기계 번역과 음성 인식 등에 혁명을 가져왔다. → Chapter 03

---

## M

### MCP Client (MCP 클라이언트)
> Model Context Protocol에서 AI 모델(호스트)과 MCP 서버 사이를 중개하는 구성요소이다. 호스트 애플리케이션 내에서 MCP 서버와의 연결을 관리하고, 도구 호출과 리소스 접근을 처리한다. → Chapter 12

### MCP Server (MCP 서버)
> Model Context Protocol에서 AI 모델에게 도구(Tools), 리소스(Resources), 프롬프트(Prompts) 등을 제공하는 서버이다. 데이터베이스 접근, 파일 시스템 조작, 외부 API 호출 등 다양한 기능을 표준화된 방식으로 AI에게 노출한다. → Chapter 12

### Memory (메모리)
> AI 에이전트가 이전 상호작용의 정보를 저장하고 활용하는 능력이다. 단기 메모리(현재 대화 컨텍스트)와 장기 메모리(이전 세션의 정보 저장)로 구분되며, 에이전트의 연속적이고 일관된 작업 수행에 핵심적인 역할을 한다. → Chapter 11

### Mixture of Experts (MoE, 전문가 혼합)
> 여러 개의 전문가 네트워크(expert)와 이를 선택하는 게이팅 네트워크로 구성된 아키텍처이다. 각 입력에 대해 소수의 전문가만 활성화되므로, 전체 파라미터 수는 크지만 실제 계산량은 적다. 효율적인 대규모 모델 확장 방법으로 주목받고 있다. → Chapter 19

### Multi-Agent (멀티 에이전트)
> 여러 AI 에이전트가 서로 다른 역할을 맡아 협업하여 복잡한 작업을 수행하는 시스템이다. 각 에이전트가 전문 분야를 담당하고 상호 소통하며, CrewAI, AutoGen 등의 프레임워크가 이를 지원한다. → Chapter 11

### Multi-Head Attention (멀티헤드 어텐션)
> Self-Attention을 여러 개의 독립적인 "헤드"로 병렬 수행한 뒤 결과를 결합하는 메커니즘이다. 각 헤드가 입력의 서로 다른 관점(문법적 관계, 의미적 유사성 등)에 집중할 수 있어, 단일 어텐션보다 풍부한 표현을 학습한다. → Chapter 05

---

## N

### Next Token Prediction (다음 토큰 예측)
> LLM의 핵심 학습 목표로, 주어진 이전 토큰들의 시퀀스로부터 바로 다음에 올 토큰의 확률 분포를 예측하는 과제이다. 이 단순한 목표만으로 모델이 문법, 사실 지식, 추론 능력까지 학습하게 되는 것이 LLM의 놀라운 특성이다. → Chapter 06

### Neural Network (신경망)
> 인간 뇌의 뉴런 연결 구조에서 영감을 받아, 퍼셉트론을 여러 층으로 쌓아 복잡한 패턴을 학습하는 수학적 모델이다. 입력층, 은닉층, 출력층으로 구성되며, 역전파와 경사 하강법으로 학습한다. 현대 AI의 근간이 되는 구조이다. → Chapter 03

---

## O

### Overfitting (과적합)
> 모델이 훈련 데이터에 지나치게 맞춰져서 새로운 데이터에 대한 일반화 능력이 떨어지는 현상이다. 훈련 정확도는 매우 높지만 검증/테스트 정확도가 낮은 것이 특징이며, 테스트 케이스를 외워버린 코드에 비유할 수 있다. 정규화, 드롭아웃, 데이터 증강 등으로 방지한다. → Chapter 02

---

## P

### Parameter (파라미터)
> 신경망에서 학습을 통해 조정되는 가중치(weight)와 편향(bias) 값의 총칭이다. LLM의 규모를 나타내는 핵심 지표로, GPT-3는 1,750억 개, 최신 모델은 수조 개의 파라미터를 보유한다. 파라미터가 많을수록 더 복잡한 패턴을 학습할 수 있지만 더 많은 계산 자원이 필요하다. → Chapter 06

### PEFT (Parameter-Efficient Fine-Tuning, 파라미터 효율적 미세조정)
> 모델 전체 파라미터를 수정하지 않고 소수의 파라미터만 조정하여 미세조정하는 기법의 총칭이다. LoRA, Prefix Tuning, Adapter 등이 포함되며, 계산 비용과 메모리 사용을 대폭 줄이면서 효과적인 모델 적응을 가능하게 한다. → Chapter 07

### Perceptron (퍼셉트론)
> 가장 단순한 형태의 인공 뉴런으로, 입력에 가중치를 곱해 합산한 뒤 활성화 함수를 적용하여 출력을 생성하는 구조이다. 1957년 Frank Rosenblatt가 제안했으며, 직선 하나로 분리 가능한 문제만 풀 수 있다는 한계가 있어 다층 신경망으로 발전했다. → Chapter 03

### Perplexity (퍼플렉시티)
> 언어 모델의 성능을 평가하는 지표로, 모델이 다음 토큰을 얼마나 잘 예측하는지를 나타낸다. 낮을수록 모델의 예측이 정확하다는 의미이며, 직관적으로는 모델이 각 위치에서 고려해야 하는 후보 토큰 수의 평균으로 해석할 수 있다. → Chapter 06

### Planning (계획)
> AI 에이전트가 복잡한 작업을 더 작은 하위 작업으로 분해하고 실행 순서를 결정하는 능력이다. 효과적인 계획 수립은 에이전트가 장기적이고 다단계적인 목표를 달성하는 데 필수적이다. → Chapter 11

### Positional Encoding (위치 인코딩)
> Transformer에서 토큰의 순서 정보를 표현하기 위해 임베딩에 추가하는 벡터이다. RNN과 달리 Transformer는 모든 토큰을 동시에 처리하므로, 별도의 위치 정보가 없으면 단어의 순서를 알 수 없다. 사인/코사인 함수나 학습 가능한 벡터를 사용한다. → Chapter 05

### Pre-training (사전학습)
> LLM이 대규모 텍스트 데이터를 사용하여 언어의 일반적 패턴, 문법, 사실 지식을 습득하는 초기 학습 단계이다. 다음 토큰 예측(GPT) 또는 마스크 토큰 예측(BERT) 등의 자기지도학습 방식으로 진행되며, 이후 미세조정의 기반이 된다. → Chapter 07

### Prompt-Driven Development (프롬프트 주도 개발)
> 자연어 프롬프트를 소프트웨어 명세서로 활용하여 AI가 코드를 생성하게 하는 개발 방법론이다. 프롬프트의 품질이 생성되는 코드의 품질을 직접 결정하며, AI 네이티브 개발 패러다임의 핵심 실천 방식이다. → Chapter 15

### Prompt Injection (프롬프트 인젝션)
> 악의적인 입력을 통해 LLM의 시스템 프롬프트를 우회하거나 의도하지 않은 행동을 유발하는 보안 공격이다. SQL 인젝션과 유사한 개념으로, LLM 기반 시스템의 주요 보안 위협 중 하나이다. → Chapter 09

---

## Q

### Quantization (양자화)
> 모델의 가중치를 더 낮은 정밀도(FP32 → FP16 → INT8 → INT4)로 변환하여 모델 크기와 메모리 사용량을 줄이는 기법이다. 약간의 성능 저하를 감수하고 추론 속도를 높이며, 모바일이나 에지 디바이스에서의 모델 실행을 가능하게 한다. → Chapter 08

---

## R

### RAG (Retrieval-Augmented Generation, 검색 증강 생성)
> 외부 지식 소스에서 관련 정보를 검색하여 LLM의 입력에 포함시킴으로써 응답 품질을 향상시키는 기법이다. LLM의 환각(Hallucination) 문제와 지식 단절 문제를 완화하며, 최신 정보와 도메인 특화 지식을 활용할 수 있게 한다. → Chapter 10

### Rate Limiting (속도 제한)
> API 서비스에서 일정 시간 내 허용되는 요청 수를 제한하는 정책이다. LLM API에서는 분당 요청 수(RPM)와 분당 토큰 수(TPM) 등으로 제한이 적용되며, 서비스 안정성과 공정한 자원 배분을 위해 사용된다. → Chapter 14

### ReAct (Reasoning + Acting)
> LLM이 추론(Reasoning)과 행동(Acting)을 번갈아 수행하는 프롬프팅 패턴이다. 생각(Thought) → 행동(Action) → 관찰(Observation)의 순환을 통해 외부 도구를 활용하면서 복잡한 문제를 단계적으로 해결한다. → Chapter 09

### Red Teaming (레드 팀)
> AI 시스템의 취약점, 편향, 유해한 출력을 발견하기 위해 의도적으로 적대적 테스트를 수행하는 평가 방법이다. 모델을 악용하려는 시나리오를 시뮬레이션하여 배포 전 안전성을 검증한다. → Chapter 16

### Regularization (정규화)
> 과적합을 방지하기 위해 모델의 복잡도에 페널티를 부여하는 기법의 총칭이다. L1 정규화(Lasso), L2 정규화(Ridge), 드롭아웃, 조기 종료(Early Stopping) 등이 포함된다. 훈련 데이터에 과도하게 맞추는 것을 억제하여 일반화 성능을 높인다. → Chapter 02

### Reinforcement Learning (강화학습)
> 에이전트(Agent)가 환경(Environment)과 상호작용하며 보상(Reward)을 최대화하는 행동을 학습하는 머신러닝 패러다임이다. AlphaGo에서의 성공이 유명하며, RLHF를 통해 LLM 학습에도 핵심적으로 활용된다. → Chapter 02

### Reranking (재정렬)
> RAG 파이프라인에서 1차 검색으로 얻은 문서 목록을 더 정밀한 모델로 재평가하여 순서를 조정하는 과정이다. 초기 검색의 recall(재현율)과 정밀한 재정렬의 precision(정밀도)을 결합하여 최종 검색 품질을 높인다. → Chapter 10

### Residual Connection (잔차 연결)
> 층의 입력을 출력에 직접 더하는 연결 구조로, `output = F(x) + x` 형태이다. 기울기가 층을 건너뛸 수 있는 "지름길"을 제공하여 기울기 소실 문제를 완화하고, 매우 깊은 네트워크의 학습을 가능하게 한다. ResNet에서 처음 도입되었고, Transformer에서도 핵심 구성요소이다. → Chapter 03, Chapter 05

### RLHF (Reinforcement Learning from Human Feedback, 인간 피드백 기반 강화학습)
> 인간 평가자의 선호도 데이터를 사용하여 보상 모델을 학습하고, 이를 기반으로 LLM의 출력을 인간의 기대에 맞게 조정하는 학습 기법이다. ChatGPT가 유용하고 안전한 응답을 생성하도록 학습된 핵심 방법이다. → Chapter 07

### RNN (Recurrent Neural Network, 순환 신경망)
> 이전 시점의 은닉 상태(hidden state)를 다음 시점의 입력과 함께 처리하여 순차 데이터를 다루는 신경망이다. 텍스트, 음성, 시계열 등 순서가 중요한 데이터에 사용되지만, 장기 의존성 문제와 병렬 처리 불가라는 한계가 있어 Transformer에 의해 대부분 대체되었다. → Chapter 03

---

## S

### Scaling Law (스케일링 법칙)
> 모델 크기, 데이터 양, 컴퓨팅 자원과 모델 성능 사이의 예측 가능한 관계를 기술하는 법칙이다. 일반적으로 이 세 가지 요소를 늘리면 성능이 멱급수적으로 향상되며, 이 발견이 대규모 모델 개발 투자의 이론적 근거가 되었다. → Chapter 06

### SDK (Software Development Kit, 소프트웨어 개발 키트)
> 특정 플랫폼이나 서비스를 위한 개발 도구, 라이브러리, 문서의 모음이다. LLM 분야에서는 Anthropic SDK, OpenAI SDK 등 모델 API를 프로그래밍 언어에서 쉽게 호출할 수 있게 해주는 클라이언트 라이브러리를 의미한다. → Chapter 14

### Self-Attention (셀프 어텐션)
> 하나의 시퀀스 내에서 각 토큰이 다른 모든 토큰과의 관계를 계산하는 어텐션 메커니즘이다. 입력 자체에서 Query, Key, Value를 생성하며, "Attention Is All You Need" 논문에서 Transformer의 핵심 구성요소로 제안되었다. → Chapter 05

### Seq2Seq (Sequence-to-Sequence, 시퀀스 투 시퀀스)
> 가변 길이의 입력 시퀀스를 가변 길이의 출력 시퀀스로 변환하는 모델 구조이다. 인코더가 입력을 문맥 벡터로 압축하고, 디코더가 이를 받아 출력을 생성한다. 기계 번역, 텍스트 요약 등에 사용되었으며, 현대 LLM 구조의 역사적 기원이다. → Chapter 04

### Sequential Thinking (순차적 사고)
> MCP 환경에서 AI 모델이 복잡한 문제를 구조화된 단계별 사고 과정으로 처리할 수 있게 지원하는 기능이다. 문제 분해, 단계적 추론, 결과 종합의 과정을 명시적으로 관리한다. → Chapter 12

### SFT (Supervised Fine-Tuning, 지도 미세조정)
> 사전학습된 LLM을 고품질의 입력-출력 쌍 데이터로 추가 학습시키는 과정이다. RLHF 이전 단계로, 모델이 지시를 따르고 대화형 응답을 생성하도록 학습시킨다. 학습 데이터의 품질이 결과에 직접적 영향을 미친다. → Chapter 07

### Speculative Decoding (투기적 디코딩)
> 작고 빠른 모델(draft model)이 먼저 여러 토큰을 생성하고, 큰 모델이 이를 한 번에 검증하는 방식으로 추론 속도를 높이는 기법이다. 큰 모델의 출력 품질을 유지하면서도 지연 시간을 크게 줄일 수 있다. → Chapter 08

### SSM (State Space Model, 상태 공간 모델)
> 연속 시간 상태 공간 이론에 기반한 시퀀스 모델링 아키텍처이다. Mamba가 대표적이며, Transformer의 Self-Attention이 시퀀스 길이에 대해 이차적(O(n^2))으로 증가하는 문제를 선형(O(n))으로 해결하여 긴 시퀀스 처리에 유리하다. Transformer의 대안으로 주목받고 있다. → Chapter 19

### Streaming (스트리밍)
> LLM의 응답을 생성되는 즉시 토큰 단위로 실시간 전송하는 방식이다. 전체 응답이 완성될 때까지 기다리지 않고 사용자에게 점진적으로 텍스트를 보여주어 체감 응답 시간을 크게 줄인다. Server-Sent Events(SSE)가 일반적으로 사용된다. → Chapter 14

### Supervised Learning (지도학습)
> 입력(Feature)과 그에 대응하는 정답(Label)의 쌍으로 구성된 데이터를 통해 패턴을 학습하는 머신러닝 방식이다. 분류(Classification)와 회귀(Regression)로 나뉘며, 가장 널리 사용되는 머신러닝 패러다임이다. → Chapter 02

### System Prompt (시스템 프롬프트)
> LLM의 행동 방식, 역할, 제약 조건 등을 정의하는 초기 프롬프트이다. 사용자에게는 보이지 않지만 모델의 모든 응답에 영향을 미치며, AI 애플리케이션의 성격과 품질을 결정하는 핵심 요소이다. → Chapter 09

---

## T

### Temperature (온도)
> LLM의 텍스트 생성 시 확률 분포의 무작위성을 조절하는 매개변수이다. 0에 가까우면 가장 확률 높은 토큰만 선택하여 결정적(deterministic)인 출력을 만들고, 높을수록 다양하고 창의적인 출력을 생성하지만 일관성이 떨어질 수 있다. → Chapter 08

### TF-IDF (Term Frequency-Inverse Document Frequency)
> 단어의 문서 내 빈도(TF)와 전체 문서 집합에서의 희귀도(IDF)를 결합하여 단어의 중요도를 산출하는 전통적 텍스트 표현 방법이다. 모든 문서에 나타나는 일반적 단어의 가중치는 낮추고, 특정 문서에만 나타나는 고유한 단어의 가중치를 높인다. → Chapter 04

### Tokenization (토큰화)
> 텍스트를 모델이 처리할 수 있는 최소 의미 단위(토큰)로 분리하는 과정이다. BPE, WordPiece, SentencePiece 등의 알고리즘이 사용되며, 토큰 수가 API 비용, 컨텍스트 윈도우, 처리 속도에 직접 영향을 미친다. → Chapter 04

### Tool Use (도구 사용)
> LLM이 텍스트 생성 외에 계산기, 검색 엔진, 코드 실행기, 데이터베이스 등 외부 도구를 호출하여 활용하는 능력이다. Function Calling과 밀접하게 관련되며, AI 에이전트가 실제 작업을 수행할 수 있게 하는 핵심 기반이다. → Chapter 11

### Top-k
> 텍스트 생성 시 확률이 가장 높은 상위 k개의 토큰만을 후보로 남기고, 그 중에서 샘플링하는 디코딩 전략이다. k 값이 작으면 보수적이고 일관된 출력을, 크면 다양한 출력을 생성한다. 저확률의 이상한 토큰이 선택되는 것을 방지한다. → Chapter 08

### Top-p (Nucleus Sampling, 핵심 샘플링)
> 누적 확률이 p에 도달할 때까지의 토큰만 후보로 유지하는 디코딩 전략이다. Top-k와 달리 확률 분포에 따라 후보 수가 동적으로 조절되므로, 확실한 상황에서는 적은 후보를, 불확실한 상황에서는 많은 후보를 고려한다. → Chapter 08

### TPU (Tensor Processing Unit, 텐서 처리 장치)
> Google이 딥러닝 학습과 추론을 위해 자체 설계한 전용 AI 가속기 칩이다. 행렬 연산에 최적화되어 있으며, Google의 클라우드 인프라와 Gemini 등의 모델 학습에 활용된다. GPU의 대안으로 사용되는 AI 전용 하드웨어이다. → Chapter 14

### Transformer (트랜스포머)
> 2017년 Google의 "Attention Is All You Need" 논문에서 제안된 신경망 아키텍처이다. Self-Attention 메커니즘을 핵심으로 하여 RNN 없이도 시퀀스를 처리할 수 있으며, 병렬 처리가 가능하여 GPU의 장점을 극대화한다. GPT, BERT, Claude, Gemini 등 현대 AI의 거의 모든 기반이 되는 구조이다. → Chapter 05

### Transport Protocol (전송 프로토콜)
> MCP에서 클라이언트와 서버 간 통신에 사용되는 프로토콜이다. 로컬 프로세스 간 통신을 위한 stdio와 원격 통신을 위한 HTTP/SSE(Server-Sent Events)가 주요 전송 방식이다. → Chapter 12

---

## U

### Underfitting (과소적합)
> 모델이 데이터의 패턴을 충분히 학습하지 못한 상태이다. 훈련 데이터와 검증 데이터 모두에서 성능이 낮으며, 모델이 너무 단순하거나 학습이 부족할 때 발생한다. 모델 복잡도를 높이거나 학습을 더 진행하여 해결한다. → Chapter 02

### Unsupervised Learning (비지도학습)
> 정답 레이블 없이 데이터 자체의 구조와 패턴을 발견하는 머신러닝 방식이다. 군집화(Clustering)로 유사한 데이터를 그룹핑하거나, 차원 축소(Dimensionality Reduction)로 핵심 특성을 추출하는 것이 대표적이다. → Chapter 02

---

## V

### Variance (분산)
> 모델이 훈련 데이터의 작은 변화에 민감하게 반응하는 불안정성의 정도이다. 높은 분산은 과적합의 원인이 되며, 훈련 데이터를 조금만 바꿔도 완전히 다른 모델이 나오는 현상으로 나타난다. 편향(Bias)과 함께 편향-분산 트레이드오프를 구성한다. → Chapter 02

### Vector Database (벡터 데이터베이스)
> 고차원 벡터(임베딩)의 저장, 인덱싱, 유사도 검색에 특화된 데이터베이스이다. Pinecone, Weaviate, ChromaDB, pgvector 등이 대표적이며, RAG 파이프라인에서 의미 기반 문서 검색을 위한 핵심 인프라이다. → Chapter 10

### Vibe Coding (바이브 코딩)
> 자연어로 원하는 바를 설명하면 AI가 코드를 생성하는 개발 방식이다. 코드의 세부 구현보다 원하는 결과의 "분위기(vibe)"를 전달하는 것이 핵심이며, AI 코딩 도구의 발전과 함께 등장한 새로운 개발 패러다임이다. → Chapter 15

### vLLM
> 대규모 언어 모델의 추론을 효율적으로 서빙하기 위한 오픈소스 추론 엔진이다. PagedAttention 기법을 도입하여 KV Cache의 메모리 관리를 최적화하고, 연속 배칭(continuous batching)으로 처리량(throughput)을 크게 향상시킨다. → Chapter 14

### VRAM (Video RAM, 비디오 램)
> GPU에 탑재된 전용 메모리로, LLM의 학습과 추론 시 모델 가중치, KV Cache, 활성값 등을 저장하는 데 사용된다. VRAM 용량이 실행 가능한 모델 크기를 결정하는 핵심 제약 요소이며, 양자화(Quantization) 등으로 VRAM 사용을 줄일 수 있다. → Chapter 14

---

## W

### Word2Vec
> 2013년 Google의 Tomas Mikolov가 발표한 단어 임베딩 모델로, 주변 단어를 예측(Skip-gram)하거나 주변 단어로 중심 단어를 예측(CBOW)하는 방식으로 단어 벡터를 학습한다. "King - Man + Woman = Queen" 같은 벡터 연산이 가능하다는 발견으로 NLP 분야에 혁명을 일으켰다. → Chapter 04

---

## Z

### Zero-shot (제로샷)
> 특정 작업에 대한 예시를 전혀 제공하지 않고, 작업 설명만으로 모델이 해당 작업을 수행하게 하는 방식이다. 사전학습 과정에서 습득한 일반 지식만으로 새로운 작업을 처리하는 능력이며, 모델이 클수록 제로샷 성능이 향상되는 경향이 있다. → Chapter 09

---

## 빠른 참조 색인

> 각 챕터에서 주로 다루는 용어를 한눈에 확인할 수 있습니다.

| 챕터 | 주요 용어 |
|------|-----------|
| **Ch.01** AI의 역사 | AI Winter, Symbolic AI, Expert System, Connectionism, Turing Test |
| **Ch.02** 머신러닝 기초 | Supervised Learning, Unsupervised Learning, Reinforcement Learning, Feature, Label, Loss Function, Gradient, Overfitting, Underfitting, Bias, Variance, Regularization, Cross-Validation, Batch Size, Learning Rate, Epoch |
| **Ch.03** 신경망과 딥러닝 | Perceptron, Neural Network, Activation Function, Backpropagation, Gradient Descent, CNN, RNN, LSTM, GRU, Dropout, Batch Normalization, Residual Connection |
| **Ch.04** NLP 기초 | Tokenization, BPE, Embedding, Word2Vec, GloVe, Bag of Words, TF-IDF, Seq2Seq, Encoder, Decoder, Attention |
| **Ch.05** Transformer | Self-Attention, Multi-Head Attention, Positional Encoding, Transformer, Residual Connection |
| **Ch.06** LLM 심층 분석 | LLM, GPT, BERT, Parameter, Context Window, Scaling Law, Emergent Ability, Perplexity, Next Token Prediction, Autoregressive |
| **Ch.07** LLM 학습 | Pre-training, Fine-tuning, SFT, RLHF, DPO, Constitutional AI, LoRA, PEFT, Data Curation, Alignment |
| **Ch.08** 추론 | Inference, Temperature, Top-k, Top-p, Beam Search, Greedy Decoding, KV Cache, Speculative Decoding, Quantization |
| **Ch.09** 프롬프트 엔지니어링 | System Prompt, Few-shot, Zero-shot, Chain-of-Thought, Prompt Injection, ReAct |
| **Ch.10** RAG | RAG, Vector Database, Chunking, Embedding Search, Hybrid Search, Reranking, Hallucination |
| **Ch.11** AI 에이전트 | Tool Use, Function Calling, Agent Loop, Planning, Memory, Multi-Agent, Agentic AI |
| **Ch.12** MCP | MCP Server, MCP Client, Transport Protocol, Sequential Thinking |
| **Ch.14** 인프라 | GPU, CUDA, VRAM, TPU, vLLM, API, SDK, Rate Limiting, Streaming |
| **Ch.15** 개발 방법론 | Vibe Coding, Prompt-Driven Development, Human-in-the-Loop |
| **Ch.16** LLMOps | Benchmark, Guardrails, Red Teaming |
| **Ch.17** 멀티모달 | Diffusion Model |
| **Ch.18** 추론 시간 컴퓨팅 | Extended Thinking, Chain-of-Thought |
| **Ch.19** 트렌드 | MoE, SSM, AGI, ASI, Alignment, AI Safety, Agentic AI |

---

*이 용어 사전은 본 가이드의 학습 과정에서 지속적으로 참조할 수 있도록 구성되었습니다. 각 용어의 정의는 본 가이드의 맥락에 맞게 작성되었으며, 보다 깊은 이해를 위해 해당 챕터를 참고하시기 바랍니다.*
