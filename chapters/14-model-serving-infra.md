# Chapter 14: 모델 서빙과 인프라

> **이 장의 대상 독자:** 매일 AI API를 호출하지만, 그 뒤에서 어떤 하드웨어와 소프트웨어가
> 동작하는지 모르는 개발자

---

## 핵심 키워드 요약

| 키워드 | 정의 |
|--------|------|
| **API (Application Programming Interface)** | 클라이언트가 LLM과 통신하기 위한 표준화된 인터페이스 |
| **SDK (Software Development Kit)** | API 호출을 편리하게 감싸주는 언어별 라이브러리 |
| **Streaming (SSE)** | 서버가 토큰을 생성할 때마다 즉시 전송하는 실시간 응답 방식 |
| **vLLM** | PagedAttention 기법으로 GPU 메모리를 효율적으로 관리하는 서빙 프레임워크 |
| **TensorRT-LLM** | NVIDIA가 제공하는 LLM 추론 최적화 엔진 |
| **Ollama** | 로컬 환경에서 LLM을 간편하게 실행할 수 있게 해주는 도구 |
| **llama.cpp** | CPU에서도 LLM 추론이 가능하도록 C/C++로 작성된 경량 런타임 |
| **GPU (Graphics Processing Unit)** | 수천 개의 코어로 병렬 연산을 수행하는 AI 핵심 가속기 |
| **CUDA** | NVIDIA GPU에서 범용 병렬 연산을 수행하기 위한 프로그래밍 모델 |
| **VRAM (Video RAM)** | GPU에 탑재된 고속 메모리. LLM 서빙에서 가장 큰 병목 |
| **TPU (Tensor Processing Unit)** | Google이 자체 설계한 AI 전용 가속기 |
| **Rate Limiting** | API 제공자가 사용량을 제한하는 메커니즘 |
| **TTFT (Time To First Token)** | 요청 후 첫 번째 토큰이 도착하기까지의 지연 시간 |
| **PagedAttention** | 운영체제의 가상 메모리 기법을 KV 캐시에 적용한 메모리 관리 기법 |

---

## 1. LLM API의 구조

### 1.1 요청/응답 형식 (Request/Response Format)

여러분이 매일 사용하는 Claude, ChatGPT 같은 AI 서비스는 내부적으로 REST API 호출로
동작합니다. 개발자에게 익숙한 HTTP 요청/응답 패턴을 따르지만, LLM 특유의 구조가 있습니다.

```json
// POST /v1/messages (Anthropic API 예시)
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "당신은 친절한 한국어 코딩 튜터입니다.",
  "messages": [
    {"role": "user", "content": "Python으로 퀵소트 구현해줘"},
    {"role": "assistant", "content": "네, 퀵소트를 구현해 드리겠습니다."},
    {"role": "user", "content": "시간 복잡도도 설명해줘"}
  ],
  "temperature": 0.7,
  "top_p": 0.9
}
```

핵심 구성 요소를 하나씩 살펴보겠습니다.

- **model**: 사용할 모델의 식별자. 같은 제공자라도 여러 모델을 제공합니다.
- **system**: 시스템 프롬프트. 모델의 행동 방식을 지시하는 "사전 명령"입니다.
- **messages**: 대화 이력. `role`과 `content`의 배열로 구성됩니다.
- **temperature**: 0에 가까울수록 결정적(deterministic), 1에 가까울수록 창의적 응답.
- **top_p**: 누적 확률 기반 샘플링 범위. temperature와 함께 출력 다양성을 조절합니다.
- **max_tokens**: 생성할 최대 토큰 수. 비용과 직결되므로 적절히 설정해야 합니다.

응답은 다음과 같은 구조로 돌아옵니다.

```json
{"id": "msg_01XFDUDYJgAACzvnptvVoYEL", "type": "message", "role": "assistant",
 "content": [{"type": "text", "text": "퀵소트의 시간 복잡도는..."}],
 "model": "claude-sonnet-4-20250514",
 "usage": {"input_tokens": 142, "output_tokens": 387}}
```

`usage` 필드에 주목하세요. 이 숫자가 곧 청구 금액을 결정합니다.

### 1.2 스트리밍 응답 (Server-Sent Events)

일반 API 호출은 모든 토큰이 생성될 때까지 기다린 후 한 번에 응답합니다.
하지만 LLM은 토큰을 하나씩 순차적으로 생성하므로, 전체 응답을 기다리면
사용자 경험이 나빠집니다. 이를 해결하는 것이 **SSE(Server-Sent Events)** 기반
스트리밍입니다.

```
data: {"type":"content_block_delta","delta":{"text":"퀵"}}
data: {"type":"content_block_delta","delta":{"text":"소트"}}
data: {"type":"content_block_delta","delta":{"text":"의"}}
data: {"type":"content_block_delta","delta":{"text":" 평균"}}
...
data: {"type":"message_stop"}
```

일반 요청이 파일 전체를 다운로드하는 것이라면, 스트리밍은 동영상 스트리밍과 같습니다.
데이터가 준비되는 즉시 전달됩니다.

```python
# Python SDK 스트리밍 예시
import anthropic

client = anthropic.Anthropic()

with client.messages.stream(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "퀵소트 설명해줘"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)  # 토큰이 도착할 때마다 출력
```

### 1.3 토큰 카운팅과 과금 (Token Counting and Billing)

LLM API의 과금 단위는 **토큰(token)** 입니다. 토큰은 단어보다 작은 단위로,
대략적인 기준은 다음과 같습니다.

```
영어: 1 토큰 ≈ 4글자 ≈ 0.75 단어
한국어: 1 토큰 ≈ 1~2글자 (한국어는 토큰 효율이 낮음)
코드: 변수명, 괄호, 연산자 등이 각각 토큰으로 분리됨
```

**중요**: 한국어는 영어보다 같은 의미를 전달하는 데 더 많은 토큰을 소비합니다.
이는 대부분의 토크나이저(Tokenizer)가 영어 중심으로 학습되었기 때문입니다.

---

## 2. 모델 서빙 프레임워크 (Model Serving Frameworks)

LLM을 실제 서비스로 배포하려면 단순히 모델을 로딩하는 것 이상의 최적화가 필요합니다.
여기서 서빙 프레임워크가 등장합니다.

### 2.1 vLLM -- PagedAttention으로 메모리 효율 극대화

vLLM은 UC Berkeley에서 개발한 오픈소스 LLM 서빙 엔진입니다.
핵심 혁신은 **PagedAttention** 기법에 있습니다.

운영체제가 가상 메모리(Virtual Memory)에서 페이지 테이블을 사용해 물리 메모리를
효율적으로 관리하듯, PagedAttention은 KV 캐시(Key-Value Cache)를 고정 크기의
블록으로 나눠 관리합니다.

```
기존 방식:                          PagedAttention:
+---------------------------+       +-------+-------+-------+
| 요청 1의 KV 캐시 (연속)    |       | 블록1 | 블록3 | 블록5 |  <- 요청 1
+---------------------------+       +-------+-------+-------+
|    낭비되는 빈 공간         |       | 블록2 | 블록4 |       |  <- 요청 2
+---------------------------+       +-------+-------+-------+
| 요청 2의 KV 캐시 (연속)    |       (비연속적 블록 할당, 낭비 최소화)
+---------------------------+
```

이를 통해 기존 대비 **2~4배의 처리량(throughput)** 향상을 달성합니다.

```bash
# vLLM 서버 실행 예시
pip install vllm
python -m vllm.entrypoints.openai.api_server \
    --model meta-llama/Llama-3.1-8B-Instruct \
    --port 8000
```

### 2.2 TensorRT-LLM -- NVIDIA의 최적화 추론 엔진

NVIDIA가 자사 GPU에 최적화하여 만든 추론 엔진입니다.
커널 퓨전(Kernel Fusion), 양자화(Quantization), 인플라이트 배칭(In-flight Batching)
등의 기법으로 NVIDIA GPU에서 최고의 성능을 끌어냅니다.

- **커널 퓨전**: 여러 GPU 연산을 하나로 합쳐 오버헤드를 줄임
- **FP8 양자화**: H100 이상에서 지원하는 8비트 부동소수점으로 메모리 사용량 절반 감소
- **인플라이트 배칭**: 생성 중인 요청과 새 요청을 동시에 처리

### 2.3 Triton Inference Server

NVIDIA의 범용 모델 서빙 서버입니다. LLM뿐 아니라 다양한 모델 형식을 지원하며,
TensorRT-LLM 백엔드와 결합하여 프로덕션 환경에서 사용됩니다.
모델 앙상블, A/B 테스트, 동적 배칭 등 엔터프라이즈 기능을 제공합니다.

### 2.4 Ollama -- 로컬에서 LLM 간편 실행

Docker가 컨테이너 실행을 단순화했듯이, Ollama는 로컬 LLM 실행을 단순화합니다.

```bash
# 설치 후 한 줄이면 모델 실행 가능
ollama run llama3.1

# API 서버로도 동작 (OpenAI 호환 API)
curl http://localhost:11434/api/generate \
  -d '{"model": "llama3.1", "prompt": "안녕하세요"}'
```

복잡한 GPU 설정, 모델 변환, 양자화 등을 자동으로 처리해줍니다.
개발자가 로컬에서 빠르게 프로토타이핑할 때 특히 유용합니다.

### 2.5 llama.cpp -- CPU에서도 LLM을 돌리다

llama.cpp는 순수 C/C++로 작성된 LLM 추론 엔진으로, GPU 없이도 CPU만으로
LLM을 실행할 수 있게 해줍니다.

```bash
# GGUF 포맷의 양자화된 모델 실행
./llama-cli -m llama-3.1-8b-q4_0.gguf -p "서울의 날씨는" -n 128
```

핵심은 **양자화(Quantization)** 입니다. 32비트 부동소수점(FP32) 가중치를
4비트(Q4) 또는 8비트(Q8)로 줄여 메모리 사용량을 대폭 절감합니다.
품질 손실은 놀라울 정도로 적습니다.

```
모델 크기 비교 (Llama 3.1 8B 기준):
FP32:  약 32 GB
FP16:  약 16 GB
Q8:    약  8 GB
Q4:    약  4 GB  <- 일반 노트북에서도 실행 가능
```

---

## 3. GPU와 LLM -- 왜 GPU가 필수인가

### 3.1 CUDA 프로그래밍 모델의 기초

CPU가 4~64개의 강력한 코어로 복잡한 순차 작업을 처리한다면,
GPU는 수천~수만 개의 작은 코어로 단순한 연산을 동시에 처리합니다.

```
CPU (순차 처리에 강함):
+========+========+========+========+
| 코어 1  | 코어 2  | 코어 3  | 코어 4  |   <- 각 코어가 강력
+========+========+========+========+

GPU (병렬 처리에 강함):
+--+--+--+--+--+--+--+--+--+--+--+--+
|  |  |  |  |  |  |  |  |  |  |  |  |   <- 수천 개의 작은 코어
+--+--+--+--+--+--+--+--+--+--+--+--+
|  |  |  |  |  |  |  |  |  |  |  |  |
+--+--+--+--+--+--+--+--+--+--+--+--+
```

**CUDA(Compute Unified Device Architecture)** 는 NVIDIA GPU에서 범용 연산을
수행하기 위한 프로그래밍 모델입니다. LLM의 핵심 연산인 행렬 곱셈(Matrix
Multiplication)은 본질적으로 병렬 처리에 적합하기 때문에 GPU에서 극적인
성능 향상을 보입니다.

```
행렬 곱셈 C = A x B:
- CPU: 각 원소를 순차적으로 계산
- GPU: 모든 원소를 동시에 계산 (이론적)

결과: 같은 연산이 GPU에서 10~100배 빠름
```

### 3.2 VRAM -- 왜 GPU 메모리가 병목인가

LLM 서빙에서 가장 큰 제약은 연산 속도가 아니라 **VRAM(GPU 메모리)** 입니다.

모델의 가중치(Weights)를 전부 VRAM에 올려야 추론이 가능합니다.
추론 중에는 KV 캐시(Key-Value Cache)가 추가로 VRAM을 소비합니다.

```
VRAM 사용량 = 모델 가중치 + KV 캐시 + 활성화 메모리 + 프레임워크 오버헤드

예시 (Llama 3.1 70B, FP16):
- 모델 가중치: ~140 GB
- KV 캐시 (배치 32, 4K 컨텍스트): ~40 GB
- 기타: ~10 GB
- 합계: ~190 GB -> H100 80GB 3장 필요
```

이것이 양자화(Quantization)가 중요한 이유입니다.
모델 가중치를 FP16에서 INT4로 양자화하면 메모리 사용량이 1/4로 줄어듭니다.

### 3.3 GPU 비교표

| 사양 | A100 | H100 | H200 | B200 |
|------|------|------|------|------|
| **아키텍처** | Ampere (2020) | Hopper (2023) | Hopper+ (2024) | Blackwell (2025) |
| **VRAM** | 80 GB HBM2e | 80 GB HBM3 | 141 GB HBM3e | 192 GB HBM3e |
| **메모리 대역폭** | 2.0 TB/s | 3.35 TB/s | 4.8 TB/s | 8.0 TB/s |
| **FP16 성능** | 312 TFLOPS | 989 TFLOPS | 989 TFLOPS | 2,250 TFLOPS |
| **FP8 성능** | 미지원 | 1,979 TFLOPS | 1,979 TFLOPS | 4,500 TFLOPS |
| **TDP (전력)** | 300W | 700W | 700W | 1,000W |
| **주요 특징** | 널리 보급된 표준 | FP8, Transformer Engine | 대용량 VRAM | 차세대 성능 |
| **대략적 가격** | ~$15,000 | ~$30,000 | ~$35,000 | ~$40,000+ |

**핵심 트렌드**: 세대가 올라갈수록 VRAM 용량과 메모리 대역폭이 급격히 증가합니다.
LLM 추론은 연산보다 메모리에 의해 병목이 발생하는(memory-bound) 작업이기 때문에,
메모리 대역폭이 성능에 직접적인 영향을 미칩니다.

### 3.4 멀티 GPU: NVLink와 InfiniBand

대형 모델은 한 장의 GPU에 올릴 수 없으므로, 여러 GPU를 연결해야 합니다.

- **NVLink**: 같은 서버 내 GPU 간 초고속 연결 (H100 기준 900 GB/s 양방향)
- **InfiniBand**: 서버 간 GPU 클러스터 연결 (400 Gb/s NDR)

모델을 여러 GPU에 분산하는 방식은 크게 두 가지입니다.
- **텐서 병렬화(Tensor Parallelism)**: 하나의 레이어를 여러 GPU에 분할 (NVLink 필요)
- **파이프라인 병렬화(Pipeline Parallelism)**: 레이어 그룹을 각 GPU에 할당

---

## 4. 대안 하드웨어 (Alternative Hardware)

### 4.1 TPU -- Google의 AI 전용 가속기

**TPU(Tensor Processing Unit)** 는 Google이 2016년부터 자체 설계한 AI 전용 칩입니다.
NVIDIA GPU와 달리 행렬 연산에만 특화되어 있으며, Google Cloud에서만 사용할 수 있습니다.

- **MXU(Matrix Multiply Unit)**: 대규모 행렬 곱셈에 최적화된 시스톨릭 어레이
- **HBM 메모리**: GPU와 마찬가지로 고대역폭 메모리 사용
- **TPU Pod**: 수천 개의 TPU를 고속 인터커넥트로 연결한 클러스터
- Google의 Gemini 모델이 TPU에서 학습됨

### 4.2 Trainium/Inferentia -- AWS의 자체 칩

AWS가 설계한 AI 전용 칩으로, AWS 클라우드에서만 사용 가능합니다.

- **Trainium**: 학습(Training) 전용. 가격 대비 성능에서 경쟁력 확보 목표
- **Inferentia**: 추론(Inference) 전용. 낮은 지연 시간과 높은 처리량에 초점
- NeuronSDK를 통해 PyTorch/JAX 모델을 변환하여 실행

### 4.3 AMD MI300X

AMD의 데이터센터용 AI 가속기로, NVIDIA의 독점에 도전하는 제품입니다.

- **192 GB HBM3 메모리**: H100 대비 2.4배 큰 VRAM
- **5.3 TB/s 메모리 대역폭**: 큰 모델을 단일 GPU에 올리는 데 유리
- ROCm 소프트웨어 스택을 사용 (CUDA 생태계 대비 성숙도는 아직 부족)

### 4.4 Apple Silicon (M 시리즈) -- 로컬 추론의 강자

Apple의 M1/M2/M3/M4 시리즈 칩은 통합 메모리 아키텍처(Unified Memory)를
채택하여 CPU와 GPU가 동일한 메모리를 공유합니다.

일반 PC에서는 CPU 메모리(RAM)와 GPU 메모리(VRAM)가 분리되어 있어 데이터 복사가
필요하지만, Apple Silicon은 CPU, GPU, NPU가 하나의 통합 메모리를 공유합니다.

- M4 Max 기준 최대 128 GB 통합 메모리로 꽤 큰 모델도 로컬 실행 가능
- MLX 프레임워크를 통한 Apple Silicon 최적화 추론
- llama.cpp의 Metal 백엔드를 통해 GPU 가속 지원
- 데이터센터 GPU 대비 성능은 떨어지지만, 개발/프로토타이핑에 탁월

---

## 5. 요금 체계와 Rate Limiting

### 5.1 API 과금 방식 (Token-based Pricing)

대부분의 LLM API는 **입력 토큰(Input Tokens)** 과 **출력 토큰(Output Tokens)** 을
별도로 과금합니다. 출력 토큰이 더 비싼 이유는 생성(generation)이 인코딩보다
더 많은 연산을 필요로 하기 때문입니다.

### API 가격 비교표 (2025년 기준, 100만 토큰당 USD)

| 제공자 / 모델 | 입력 가격 | 출력 가격 | 특징 |
|---------------|-----------|-----------|------|
| **Anthropic** Claude Sonnet 4 | $3.00 | $15.00 | 코딩, 분석에 강점 |
| **Anthropic** Claude Haiku 3.5 | $0.80 | $4.00 | 빠른 응답, 저비용 |
| **OpenAI** GPT-4o | $2.50 | $10.00 | 멀티모달 지원 |
| **OpenAI** GPT-4o mini | $0.15 | $0.60 | 경량 모델 |
| **Google** Gemini 1.5 Pro | $1.25 | $5.00 | 긴 컨텍스트 (최대 2M) |
| **Google** Gemini 1.5 Flash | $0.075 | $0.30 | 최저가 수준 |

> **개발자 팁**: 동일한 작업에 항상 최고 성능 모델을 쓸 필요는 없습니다.
> 분류, 요약 같은 단순 작업에는 경량 모델을, 복잡한 추론이 필요한 작업에만
> 고성능 모델을 사용하는 **모델 라우팅(Model Routing)** 전략이 비용 효율적입니다.

### 5.2 Rate Limiting과 Quota

API 제공자는 서비스 안정성을 위해 사용량을 제한합니다.

```
Rate Limit의 종류:
- RPM (Requests Per Minute): 분당 요청 수 제한
- TPM (Tokens Per Minute): 분당 토큰 수 제한
- RPD (Requests Per Day): 일일 요청 수 제한
```

대부분의 제공자는 **Tier 시스템**을 운영합니다.
사용량과 결제 이력에 따라 상위 Tier로 승격되며, 한도가 점진적으로 증가합니다.

```python
# Rate Limit 처리 예시 (지수 백오프)
import time
import anthropic

client = anthropic.Anthropic()

def call_with_retry(messages, max_retries=5):
    for attempt in range(max_retries):
        try:
            return client.messages.create(
                model="claude-sonnet-4-20250514",
                max_tokens=1024,
                messages=messages
            )
        except anthropic.RateLimitError:
            wait_time = 2 ** attempt  # 1, 2, 4, 8, 16초
            print(f"Rate limit 도달. {wait_time}초 후 재시도...")
            time.sleep(wait_time)
    raise Exception("최대 재시도 횟수 초과")
```

---

## 6. 배포 옵션 (Deployment Options)

### 6.1 클라우드 API (Cloud APIs)

가장 간편한 방식입니다. 인프라 관리 없이 API 키만으로 LLM을 사용할 수 있습니다.

| 서비스 | 특징 |
|--------|------|
| **OpenAI API** | GPT 시리즈 직접 제공. 가장 큰 생태계 |
| **Anthropic API** | Claude 시리즈 직접 제공. 안전성과 코딩에 강점 |
| **Google AI Studio** | Gemini 시리즈. 긴 컨텍스트 윈도우 |
| **AWS Bedrock** | 여러 제공자의 모델을 AWS 인프라에서 통합 제공 |
| **Azure OpenAI** | OpenAI 모델을 Azure 인프라에서 제공. 엔터프라이즈에 유리 |

### 6.2 자체 호스팅 (Self-hosted)

데이터 주권, 커스터마이징, 비용 최적화가 필요할 때 선택합니다.

자체 호스팅이 유리한 경우는 다음과 같습니다.

- 민감한 데이터가 외부로 나가면 안 되는 경우 (금융, 의료, 군사)
- 특정 도메인에 파인튜닝한 모델을 서빙하는 경우
- 대규모 트래픽으로 API 비용이 자체 운영보다 비싼 경우
- 특수한 지연 시간(latency) 요구사항이 있는 경우

일반적인 스택은 로드 밸런서 뒤에 vLLM 또는 TensorRT-LLM 서빙 노드를 배치하고,
각 노드에 H100 x 8 등의 GPU 클러스터를 연결하는 구조입니다.

### 6.3 엣지 AI와 로컬 LLM (Edge AI and Local LLMs)

모든 것을 클라우드에 보내지 않고, 디바이스에서 직접 추론하는 방식입니다.

- **프라이버시**: 데이터가 디바이스를 떠나지 않음
- **오프라인 작동**: 네트워크 없이도 AI 기능 사용 가능
- **낮은 지연 시간**: 네트워크 왕복 없이 즉시 응답

Ollama, llama.cpp, MLX 등의 도구가 이 영역을 빠르게 발전시키고 있습니다.
양자화 기술의 발전으로 7B~13B 규모의 모델을 일반 노트북에서도 실행할 수 있게
되었습니다.

---

## 7. 성능 지표 (Performance Metrics)

LLM 서빙의 성능을 평가하는 핵심 지표를 이해해야 올바른 인프라 결정을 내릴 수 있습니다.

### 7.1 핵심 지표 정리

| 지표 | 설명 |
|------|------|
| **TTFT** (Time To First Token) | 요청 후 첫 토큰이 도착하기까지의 시간. 사용자 체감 속도에 가장 큰 영향 |
| **TPS** (Tokens Per Second) | 초당 생성 토큰 수. 스트리밍 응답의 부드러움을 결정 |
| **Throughput** (처리량) | 단위 시간당 처리 가능한 전체 요청 수. 서버 효율성의 핵심 지표 |
| **Latency** (지연 시간) | 요청부터 전체 응답 완료까지의 시간. TTFT + (총 토큰 수 / TPS) |
| **GPU Utilization** (GPU 활용률) | GPU 연산 자원의 활용률. 낮으면 자원 낭비, 높으면 효율적 |

### 7.2 지표 간의 관계

```
|<--- TTFT --->|<-------- 토큰 생성 구간 -------->|
[요청 전송]  [첫 토큰]  [토큰...]  [토큰...]  [완료]
  t=0        t=200ms                          t=2000ms

이 예시: TTFT=200ms, TPS=50 tokens/sec, 총 Latency=2000ms, 생성 토큰 수 ≈ 90개
```

### 7.3 성능 최적화 전략

| 목표 | 전략 |
|------|------|
| TTFT 단축 | 모델 양자화, 더 빠른 GPU, 프리필(prefill) 최적화 |
| TPS 향상 | 추측적 디코딩(Speculative Decoding), 배칭 최적화 |
| 처리량 증가 | 연속 배칭(Continuous Batching), 멀티 GPU 스케일 아웃 |
| 비용 절감 | 양자화, 모델 라우팅, 캐싱, 경량 모델 활용 |

**추측적 디코딩(Speculative Decoding)** 은 흥미로운 최적화 기법입니다.
작은 모델(draft model)이 먼저 여러 토큰을 빠르게 생성하고,
큰 모델(target model)이 한 번에 검증합니다. 검증 통과율이 높으면
큰 모델만 사용할 때보다 2~3배 빠른 생성 속도를 달성할 수 있습니다.

---

## 8. 정리: 개발자가 알아야 할 핵심 포인트

| 결정 사항 | 선택지 | 판단 기준 |
|-----------|--------|-----------|
| 모델 접근 방식 | API vs 자체 호스팅 vs 로컬 | 데이터 민감도, 비용, 트래픽 규모 |
| 서빙 프레임워크 | vLLM, TensorRT-LLM, Ollama, llama.cpp | GPU 종류, 사용 규모, 환경 |
| 하드웨어 | NVIDIA GPU, TPU, AMD, Apple Silicon | 예산, 클라우드 제공자, 용도 |
| 최적화 전략 | 양자화, 배칭, 캐싱, 모델 라우팅 | 성능 목표, 사용자 요구사항 |

대부분의 개발자에게는 클라우드 API로 시작하는 것이 가장 합리적입니다.
트래픽이 증가하고 요구사항이 구체화되면, 자체 호스팅이나 하이브리드 방식을
검토하면 됩니다. 어떤 방식을 선택하든 TTFT, TPS, 비용이라는 세 가지 축에서
최적의 균형점을 찾는 것이 핵심입니다.

---

> **다음 장 예고**: Chapter 15에서는 "프롬프트 엔지니어링과 실전 활용"을 다룹니다.
> LLM의 성능을 최대한 끌어내기 위한 프롬프트 설계 기법을 알아봅니다.
