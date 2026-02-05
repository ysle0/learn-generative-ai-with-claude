# Appendix C: 실습 프로젝트

> **"읽기만 해서는 절대 이해할 수 없다. 직접 만들어봐야 한다."**
>
> 이론을 배운 뒤 가장 효과적인 학습은 직접 구현해보는 것입니다.
> 아래 7개의 프로젝트를 통해 각 챕터에서 배운 개념을 체화할 수 있습니다.

---

## 프로젝트 1: nanoGPT로 Transformer 밑바닥부터 이해하기

| 항목 | 내용 |
|------|------|
| **난이도** | 초급 → 중급 |
| **관련 챕터** | Chapter 03 (신경망), Chapter 05 (Transformer), Chapter 06 (LLM) |
| **필요 환경** | Python 3.10+, PyTorch, GPU(선택, CPU도 가능) |

### 프로젝트 개요

Andrej Karpathy의 [nanoGPT](https://github.com/karpathy/nanoGPT)를 따라 구현하며 Transformer의 핵심을 직접 코딩합니다. 단순히 코드를 복사하는 것이 아니라, 각 컴포넌트가 **왜** 필요한지 이해하는 것이 목표입니다.

### 학습 목표

- Self-Attention 메커니즘을 직접 구현하며 Query, Key, Value의 역할 이해
- Positional Encoding이 없으면 어떤 일이 벌어지는지 실험
- Multi-Head Attention이 Single-Head보다 나은 이유를 눈으로 확인
- 간단한 텍스트 데이터(셰익스피어 등)로 학습시켜 텍스트 생성 체험

### 단계별 가이드

```
Step 1: 환경 설정
├── Python 가상환경 생성
├── PyTorch 설치
└── 학습 데이터 다운로드 (shakespeare_char 등)

Step 2: 핵심 컴포넌트 구현
├── 토큰화 (character-level)
├── 임베딩 레이어
├── Self-Attention (단일 헤드)
├── Multi-Head Attention
├── Feed-Forward Network
├── Transformer Block (Attention → FFN → LayerNorm)
└── 전체 GPT 모델 조립

Step 3: 학습 루프 작성
├── 데이터 배치 생성
├── Forward pass → Loss 계산
├── Backward pass → 파라미터 업데이트
└── 학습 곡선 모니터링

Step 4: 텍스트 생성 및 실험
├── Temperature 값 변경 실험 (0.1 vs 0.8 vs 1.5)
├── Top-k, Top-p 샘플링 구현
├── 모델 크기(레이어 수, 헤드 수) 변경 실험
└── 한국어 데이터로 학습해보기
```

### 추천 자료

- Andrej Karpathy YouTube: "Let's build GPT: from scratch, in code, spelled out"
- Sebastian Raschka: "Build a Large Language Model (From Scratch)"

---

## 프로젝트 2: RAG 파이프라인 구축하기

| 항목 | 내용 |
|------|------|
| **난이도** | 중급 |
| **관련 챕터** | Chapter 04 (NLP), Chapter 10 (RAG) |
| **필요 환경** | Python 3.10+, LangChain 또는 LlamaIndex, ChromaDB, OpenAI/Anthropic API 키 |

### 프로젝트 개요

자신의 문서(마크다운 파일, PDF, 코드 등)를 기반으로 RAG 시스템을 구축합니다. "내 코드베이스에 대해 질문하면 답해주는 AI"를 만드는 것이 목표입니다.

### 학습 목표

- 문서 로딩, 청킹(Chunking), 임베딩의 전체 파이프라인 이해
- 벡터 데이터베이스에 문서를 저장하고 유사도 검색 수행
- 검색 결과를 LLM 컨텍스트에 주입하는 방법 체득
- 청킹 크기와 검색 방법에 따른 성능 차이 실험

### 단계별 가이드

```
Step 1: 데이터 준비
├── 대상 문서 선택 (자신의 프로젝트 README, 문서 등)
├── 문서 로더 구현 (PDF, Markdown, 코드 파일)
└── 전처리: 불필요한 내용 제거

Step 2: 청킹 전략 실험
├── 고정 크기 청킹 (500자, 1000자)
├── 재귀적 분할 (RecursiveCharacterTextSplitter)
├── 시맨틱 청킹 (의미 단위 분할)
└── 오버랩 설정 실험

Step 3: 임베딩 및 벡터 저장
├── OpenAI text-embedding-3-small 또는 Hugging Face 모델
├── ChromaDB에 벡터 저장
├── 메타데이터 (파일명, 섹션 등) 함께 저장
└── 유사도 검색 테스트

Step 4: RAG 체인 구성
├── 검색기(Retriever) 설정
├── 프롬프트 템플릿 작성
├── LLM 호출 및 답변 생성
└── 소스 인용(출처 표시) 기능 추가

Step 5: 평가 및 개선
├── 테스트 질문 세트 작성
├── 검색 정확도 측정 (recall@k)
├── 답변 품질 평가 (faithfulness)
├── Hybrid Search (키워드 + 벡터) 적용
└── Reranker 추가
```

### 코드 시작점

```python
# 최소 RAG 파이프라인 예시
from langchain_community.document_loaders import DirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.chains import RetrievalQA

# 1. 문서 로드
loader = DirectoryLoader("./my_docs", glob="**/*.md")
documents = loader.load()

# 2. 청킹
splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
chunks = splitter.split_documents(documents)

# 3. 벡터 저장
vectorstore = Chroma.from_documents(chunks, OpenAIEmbeddings())

# 4. RAG 체인
qa = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4o-mini"),
    retriever=vectorstore.as_retriever(search_kwargs={"k": 5}),
)

# 5. 질문
result = qa.invoke("이 프로젝트의 아키텍처를 설명해줘")
print(result)
```

---

## 프로젝트 3: MCP 서버 만들기

| 항목 | 내용 |
|------|------|
| **난이도** | 중급 |
| **관련 챕터** | Chapter 11 (AI Agent), Chapter 12 (MCP) |
| **필요 환경** | Node.js 18+ 또는 Python 3.10+, Claude Code |

### 프로젝트 개요

MCP (Model Context Protocol) 서버를 직접 만들어 Claude Code에서 사용할 수 있는 커스텀 도구를 추가합니다. 예: 날씨 API 조회, DB 쿼리, 파일 분석 등.

### 학습 목표

- MCP 프로토콜의 Host-Client-Server 아키텍처 이해
- Tool, Resource, Prompt의 차이와 사용법 체득
- stdio 트랜스포트를 통한 통신 구조 이해
- Claude Code에서 직접 만든 도구 사용

### 단계별 가이드

```
Step 1: MCP SDK 설정
├── @modelcontextprotocol/sdk (TypeScript) 또는 mcp (Python) 설치
├── 기본 서버 스캐폴딩 생성
└── Claude Code의 MCP 설정 파일 확인

Step 2: 간단한 Tool 구현
├── "hello" 도구: 인사말 반환
├── "get-weather" 도구: 날씨 API 호출
├── 입력 스키마 정의 (JSON Schema)
└── 에러 처리

Step 3: Resource 구현
├── 정적 리소스: 설정 파일 내용 제공
├── 동적 리소스: DB 스키마, 시스템 정보
└── URI 기반 리소스 접근

Step 4: Claude Code 연동
├── claude_desktop_config.json 설정
├── 서버 등록 및 테스트
├── 실제 대화에서 도구 호출 확인
└── 디버깅: MCP Inspector 활용
```

### 코드 시작점 (TypeScript)

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-first-mcp-server",
  version: "1.0.0",
});

// Tool 등록
server.tool(
  "get-project-stats",
  "프로젝트의 파일 통계를 반환합니다",
  {
    path: z.string().describe("분석할 프로젝트 경로"),
  },
  async ({ path }) => {
    // 파일 수, 코드 라인 수 등 계산
    const stats = await analyzeProject(path);
    return {
      content: [{ type: "text", text: JSON.stringify(stats, null, 2) }],
    };
  }
);

// 서버 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

---

## 프로젝트 4: AI 에이전트 직접 만들기

| 항목 | 내용 |
|------|------|
| **난이도** | 중급 → 고급 |
| **관련 챕터** | Chapter 09 (프롬프트), Chapter 11 (AI Agent) |
| **필요 환경** | Python 3.10+, LangGraph, OpenAI/Anthropic API 키 |

### 프로젝트 개요

LangGraph를 사용해 ReAct 패턴의 AI 에이전트를 구현합니다. 파일을 읽고, 코드를 분석하고, 질문에 답하는 간단한 코딩 어시스턴트를 만듭니다.

### 학습 목표

- ReAct (Reasoning + Acting) 패턴의 구현 원리 이해
- Tool Use / Function Calling의 내부 동작 체험
- 에이전트 루프 (Observe → Think → Act → Repeat) 직접 구현
- Claude Code와 같은 코딩 에이전트의 작동 원리 이해

### 단계별 가이드

```
Step 1: 도구 정의
├── read_file: 파일 읽기
├── list_files: 디렉토리 목록
├── search_code: 코드 검색 (grep)
├── run_command: 셸 명령 실행 (제한된)
└── write_file: 파일 쓰기

Step 2: 에이전트 그래프 구성 (LangGraph)
├── 상태(State) 정의: messages, tool_calls, results
├── 노드 정의: LLM 호출, 도구 실행
├── 엣지 정의: 조건부 라우팅 (도구 호출 여부)
└── 그래프 컴파일

Step 3: 시스템 프롬프트 작성
├── 에이전트의 역할과 능력 정의
├── 도구 사용 가이드라인
├── 단계별 사고 지시 (CoT)
└── 안전 장치 (위험한 명령 거부 등)

Step 4: 실행 및 테스트
├── "이 프로젝트의 구조를 분석해줘"
├── "main.py에서 버그를 찾아줘"
├── "테스트 파일을 작성해줘"
└── 에이전트의 사고 과정 로깅 및 분석

Step 5: 개선
├── 메모리 추가 (대화 이력 관리)
├── 계획(Planning) 단계 추가
├── 에러 복구 로직
└── 멀티 에이전트 확장 (코더 + 리뷰어)
```

---

## 프로젝트 5: LoRA로 모델 파인튜닝

| 항목 | 내용 |
|------|------|
| **난이도** | 고급 |
| **관련 챕터** | Chapter 07 (LLM 학습) |
| **필요 환경** | Python 3.10+, Hugging Face Transformers, PEFT, GPU (최소 16GB VRAM 권장) |

### 프로젝트 개요

LoRA (Low-Rank Adaptation)를 사용해 소규모 LLM을 특정 태스크에 맞게 파인튜닝합니다. 전체 파라미터가 아닌 소수의 파라미터만 학습하여 효율적으로 모델을 커스터마이징합니다.

### 학습 목표

- LoRA의 원리 이해: 왜 전체 파라미터를 학습하지 않아도 되는가
- Hugging Face PEFT 라이브러리 사용법
- 학습 데이터셋 준비 및 포맷팅
- 파인튜닝된 모델의 성능 평가

### 단계별 가이드

```
Step 1: 모델 및 데이터 준비
├── 베이스 모델 선택 (예: meta-llama/Llama-3.2-1B)
├── 학습 데이터 준비 (instruction-response 쌍)
├── 데이터 포맷팅 (Alpaca, ChatML 등)
└── 토크나이저 설정

Step 2: LoRA 설정
├── LoRA 하이퍼파라미터 설정
│   ├── rank (r): 4, 8, 16, 32
│   ├── alpha: 16, 32
│   ├── target_modules: ["q_proj", "v_proj"]
│   └── dropout: 0.05
├── PEFT 모델 생성
└── 학습 가능 파라미터 수 확인 (전체의 0.1-1%)

Step 3: 학습
├── TrainingArguments 설정
│   ├── learning_rate, epochs, batch_size
│   ├── gradient_accumulation
│   └── evaluation strategy
├── Trainer 실행
├── 학습 로스 모니터링
└── 체크포인트 저장

Step 4: 평가 및 추론
├── LoRA 어댑터 로드
├── 베이스 모델과 성능 비교
├── 다양한 프롬프트로 테스트
└── 어댑터 머지 (merge_and_unload)
```

### 코드 시작점

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, Trainer
from peft import LoraConfig, get_peft_model, TaskType
from datasets import load_dataset

# 1. 모델 로드
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.2-1B")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.2-1B")

# 2. LoRA 설정
lora_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=8,
    lora_alpha=32,
    lora_dropout=0.05,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
)

# 3. PEFT 모델 생성
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# 출력 예: "trainable params: 3,407,872 || all params: 1,238,607,872 || 0.28%"

# 4. 학습
training_args = TrainingArguments(
    output_dir="./lora-output",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    learning_rate=2e-4,
    logging_steps=10,
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
)
trainer.train()
```

---

## 프로젝트 6: LLM 평가 파이프라인 만들기

| 항목 | 내용 |
|------|------|
| **난이도** | 중급 |
| **관련 챕터** | Chapter 16 (LLMOps) |
| **필요 환경** | Python 3.10+, 여러 LLM API 키 (OpenAI, Anthropic 등) |

### 프로젝트 개요

자체 벤치마크를 구성하고 여러 모델을 비교 평가하는 파이프라인을 만듭니다. 실제 업무에서 "어떤 모델을 쓸 것인가"를 데이터 기반으로 결정하는 능력을 기릅니다.

### 학습 목표

- LLM 평가의 다양한 측면 이해 (정확도, 비용, 속도)
- 자동 평가와 사람 평가의 차이와 조합 방법
- 프롬프트 버전 관리와 A/B 테스트
- 비용 대비 성능 최적화

### 단계별 가이드

```
Step 1: 평가 데이터셋 구성
├── 태스크별 테스트 케이스 작성
│   ├── 코드 생성 (10-20문제)
│   ├── 코드 리뷰 (10-20문제)
│   ├── 문서 요약 (10-20문제)
│   └── 질의응답 (10-20문제)
├── 기대 답변(ground truth) 작성
└── 난이도 레벨 태깅

Step 2: 평가 인프라 구축
├── 여러 모델 API 호출 래퍼
├── 응답 시간 측정
├── 토큰 사용량 추적
├── 결과 저장 (JSON/CSV)
└── 비용 계산기

Step 3: 자동 평가 메트릭 구현
├── 정확 일치 (Exact Match)
├── LLM-as-Judge (GPT-4로 답변 품질 평가)
├── 코드 실행 기반 평가 (pass@k)
└── 응답 형식 준수율

Step 4: 결과 분석 및 보고서
├── 모델별 성능 비교표
├── 비용 대비 성능 차트
├── 태스크별 강점/약점 분석
└── 최적 모델 선택 가이드라인 도출
```

---

## 프로젝트 7: Ollama로 로컬 LLM 서빙하기

| 항목 | 내용 |
|------|------|
| **난이도** | 초급 |
| **관련 챕터** | Chapter 08 (추론), Chapter 14 (모델 서빙) |
| **필요 환경** | Ollama, 최소 8GB RAM (16GB 권장), macOS/Linux/Windows |

### 프로젝트 개요

Ollama를 사용해 로컬에서 LLM을 실행하고, 다양한 모델과 양자화 옵션을 비교합니다. API 비용 없이 AI를 실험할 수 있는 환경을 구축합니다.

### 학습 목표

- 로컬 LLM 실행 환경 이해
- 양자화 (Quantization)의 실제 효과 체험
- 다양한 오픈소스 모델 비교
- API 서버로서의 로컬 LLM 활용

### 단계별 가이드

```
Step 1: Ollama 설치 및 기본 사용
├── Ollama 설치 (brew install ollama 또는 공식 사이트)
├── 첫 모델 다운로드: ollama pull llama3.2
├── 대화 시작: ollama run llama3.2
└── 기본 명령어 익히기

Step 2: 다양한 모델 비교
├── 소형: llama3.2:1b, qwen2.5:1.5b, phi-4-mini
├── 중형: llama3.2:3b, mistral, gemma2:9b
├── 대형: llama3.1:70b (GPU 필요)
└── 동일 질문으로 답변 품질 비교

Step 3: 양자화 효과 비교
├── 같은 모델의 다른 양자화 버전 다운로드
│   ├── Q4_K_M (4bit, 가장 작음)
│   ├── Q5_K_M (5bit)
│   ├── Q8_0 (8bit)
│   └── FP16 (16bit, 가장 큼)
├── 파일 크기 비교
├── 응답 속도 비교 (TPS: Tokens Per Second)
└── 답변 품질 비교

Step 4: API 서버로 활용
├── Ollama API 엔드포인트 확인 (localhost:11434)
├── curl로 API 호출 테스트
├── Python에서 API 호출
├── OpenAI 호환 API로 기존 코드에 연결
└── RAG 프로젝트와 연동 (프로젝트 2와 결합)

Step 5: 커스텀 Modelfile 작성
├── 시스템 프롬프트 커스터마이징
├── Temperature, Top-k/p 파라미터 조정
├── 커스텀 모델 생성: ollama create my-assistant
└── 특정 용도에 최적화된 모델 구성
```

### 빠른 시작 명령어

```bash
# 설치
curl -fsSL https://ollama.ai/install.sh | sh

# 모델 다운로드 및 실행
ollama pull llama3.2
ollama run llama3.2

# API로 호출
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "Transformer의 Self-Attention을 간단히 설명해줘",
  "stream": false
}'

# Python에서 사용 (OpenAI 호환)
from openai import OpenAI
client = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")
response = client.chat.completions.create(
    model="llama3.2",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

---

## 프로젝트 진행 권장 순서

```
[프로젝트 7: Ollama] ─────────────── 초급, 가장 먼저 시작
        │
        ▼
[프로젝트 1: nanoGPT] ────────────── 이론 이해 심화
        │
        ▼
[프로젝트 2: RAG] ────────────────── 실용적 응용
        │
        ├──→ [프로젝트 3: MCP] ────── 도구 확장
        │
        └──→ [프로젝트 4: Agent] ──── 에이전트 구현
                │
                ▼
        [프로젝트 6: 평가] ────────── 품질 관리
                │
                ▼
        [프로젝트 5: LoRA] ────────── 모델 커스터마이징 (최고급)
```

> **팁**: 프로젝트 7(Ollama)부터 시작하면 API 비용 걱정 없이 실험할 수 있습니다.
> 이후 프로젝트 1(nanoGPT)로 이론을 다지고, 나머지는 관심사에 따라 선택하세요.

---

*각 프로젝트의 상세 코드와 가이드는 별도의 디렉토리에서 제공될 예정입니다.*
