# 생성형 AI 학습 가이드

> "매일 쓰지만 어떻게 돌아가는지는 모르겠다" — 에서 출발하는 생성형 AI 딥다이브

## 이 가이드의 대상

- Claude Code, Gemini, Kilo Code 등 AI 코딩 도구를 매일 사용하는 개발자
- MCP, 플러그인(superclaude, opencode) 등을 활용하고 있지만 내부 동작 원리가 궁금한 사람
- "뭘 모르는지도 모르는" 백지 상태에서 체계적으로 키워드를 정리하고 싶은 사람

---

## 목차 (Table of Contents)

### Part 1: 기초 — AI는 어떻게 여기까지 왔는가

#### Chapter 01. AI의 역사와 패러다임 변화
- **키워드**: Turing Test, Symbolic AI, Expert System, AI Winter, Connectionism
- 1950s: 앨런 튜링과 "기계가 생각할 수 있는가"
- 1960-70s: 기호주의 AI (Symbolic AI) — 규칙 기반 접근
- 1980s: 전문가 시스템 (Expert System)의 부흥과 한계
- 1990-2000s: AI 겨울 (AI Winter)과 통계적 학습의 부상
- 2010s: 딥러닝 혁명의 시작 — ImageNet, AlphaGo
- 2020s: 생성형 AI 빅뱅 — GPT-3, ChatGPT, Claude

#### Chapter 02. 머신러닝 기초 개념
- **키워드**: Supervised Learning, Unsupervised Learning, Reinforcement Learning, Feature, Label, Overfitting, Underfitting, Bias-Variance Tradeoff
- 머신러닝이란 — "명시적 프로그래밍 없이 학습하는 시스템"
- 지도학습 (Supervised Learning): 입력-정답 쌍으로 학습
- 비지도학습 (Unsupervised Learning): 패턴을 스스로 발견
- 강화학습 (Reinforcement Learning): 보상 기반 학습
- 핵심 개념: 특성(Feature), 레이블(Label), 손실함수(Loss Function)
- 과적합(Overfitting)과 과소적합(Underfitting)
- 편향-분산 트레이드오프 (Bias-Variance Tradeoff)
- 학습/검증/테스트 데이터 분할

#### Chapter 03. 신경망과 딥러닝
- **키워드**: Perceptron, Neural Network, Backpropagation, Gradient Descent, Activation Function, CNN, RNN, LSTM, Vanishing Gradient
- 퍼셉트론 (Perceptron) — 인공 뉴런의 시작
- 다층 신경망 (Multi-Layer Neural Network)
- 역전파 (Backpropagation) — 신경망은 어떻게 "학습"하는가
- 경사하강법 (Gradient Descent)과 옵티마이저 (Adam, SGD)
- 활성화 함수 (Activation Function): ReLU, Sigmoid, Tanh
- CNN (Convolutional Neural Network) — 이미지 인식의 주역
- RNN (Recurrent Neural Network) — 순차 데이터 처리
- LSTM / GRU — 장기 의존성 문제 해결
- 기울기 소실 문제 (Vanishing Gradient Problem)

---

### Part 2: 핵심 — Transformer와 LLM의 내부

#### Chapter 04. 자연어처리 (NLP) 기초
- **키워드**: Tokenization, Embedding, Word2Vec, GloVe, Bag of Words, TF-IDF, Seq2Seq, Attention
- 컴퓨터는 텍스트를 어떻게 이해하는가
- 토큰화 (Tokenization): BPE, WordPiece, SentencePiece
- 임베딩 (Embedding): 단어를 벡터로 — Word2Vec, GloVe
- 전통적 방법: Bag of Words, TF-IDF
- Seq2Seq 모델과 인코더-디코더 구조
- Attention 메커니즘의 등장 — "모든 것에 집중할 필요는 없다"

#### Chapter 05. Transformer 아키텍처 — 모든 것의 시작
- **키워드**: Self-Attention, Multi-Head Attention, Positional Encoding, Feed-Forward Network, Layer Normalization, Encoder-Decoder
- "Attention Is All You Need" (2017) 논문의 핵심
- Self-Attention 메커니즘 — Query, Key, Value
- Multi-Head Attention — 여러 관점에서 동시에 보기
- Positional Encoding — 순서 정보를 어떻게 넣는가
- Feed-Forward Network와 Layer Normalization
- Encoder-Decoder 구조 vs Decoder-Only 구조
- 왜 Transformer가 RNN을 대체했는가 — 병렬처리의 힘

#### Chapter 06. 대규모 언어 모델 (LLM) 심층 분석
- **키워드**: GPT, BERT, T5, LLaMA, Claude, Gemini, Parameter, Context Window, Perplexity, Emergent Ability, Scaling Law
- LLM의 핵심 원리 — "다음 토큰 예측" (Next Token Prediction)
- 모델 계보: GPT 시리즈, BERT, T5, LLaMA, Claude, Gemini
- 파라미터(Parameter) — 수십억 개의 가중치가 의미하는 것
- 컨텍스트 윈도우 (Context Window) — 한 번에 볼 수 있는 범위
- Scaling Law — 모델이 커지면 무조건 좋아지는가
- 창발적 능력 (Emergent Ability) — 규모가 만드는 질적 변화
- Perplexity — LLM 성능을 측정하는 방법
- 오픈소스 vs 클로즈드소스 모델 생태계

#### Chapter 07. LLM은 어떻게 학습되는가
- **키워드**: Pre-training, Fine-tuning, SFT, RLHF, DPO, Constitutional AI, Data Curation, Compute Budget
- 사전학습 (Pre-training): 대규모 텍스트 데이터로 일반 지식 습득
- 데이터 큐레이션 — 학습 데이터의 품질이 모든 것을 결정한다
- 지도 미세조정 (SFT: Supervised Fine-Tuning)
- RLHF (Reinforcement Learning from Human Feedback) — 인간 선호도 학습
- DPO (Direct Preference Optimization) — RLHF의 대안
- Constitutional AI — Anthropic의 접근법 (Claude가 안전한 이유)
- 컴퓨트 예산 (Compute Budget)과 학습 비용
- Chinchilla Scaling — 최적의 모델 크기 vs 데이터 양

---

### Part 3: 실전 — 생성형 AI는 어떻게 작동하는가

#### Chapter 08. 추론 (Inference) — 모델이 답을 생성하는 과정
- **키워드**: Temperature, Top-k, Top-p, Beam Search, Greedy Decoding, KV Cache, Speculative Decoding, Quantization
- 프롬프트가 들어오면 내부에서 무슨 일이 벌어지는가
- 토큰 생성 전략: Greedy, Beam Search, Sampling
- Temperature — 창의성 vs 정확성 조절 노브
- Top-k / Top-p (Nucleus Sampling) — 확률 분포 자르기
- KV Cache — 추론 속도를 높이는 핵심 기법
- Speculative Decoding — 작은 모델로 큰 모델 가속하기
- 양자화 (Quantization): FP16, INT8, INT4 — 모델 경량화
- Batching과 Throughput 최적화

#### Chapter 09. 프롬프트 엔지니어링
- **키워드**: System Prompt, Few-shot, Zero-shot, Chain-of-Thought, Tree-of-Thought, ReAct, Prompt Injection
- System Prompt — AI의 행동을 정의하는 프레임
- Zero-shot vs Few-shot 프롬프팅
- Chain-of-Thought (CoT) — "단계별로 생각해봐"
- Tree-of-Thought (ToT) — 분기하며 탐색하기
- ReAct (Reasoning + Acting) 패턴
- 프롬프트 인젝션 (Prompt Injection) — 보안 위협과 방어
- 구조화된 출력 요청 (JSON, XML, Markdown)

#### Chapter 10. RAG (Retrieval-Augmented Generation)
- **키워드**: Vector Database, Embedding Search, Chunking, Reranking, Hybrid Search, Hallucination
- LLM의 한계 — 환각(Hallucination)과 지식 단절
- RAG란 — 검색으로 LLM을 보강하기
- 벡터 데이터베이스: Pinecone, Weaviate, ChromaDB, pgvector
- 문서 청킹 (Chunking) 전략
- 임베딩 검색 (Embedding Search) vs 키워드 검색
- 하이브리드 검색 (Hybrid Search)
- Reranking — 검색 결과 재정렬
- RAG 파이프라인 설계 패턴

#### Chapter 11. AI 에이전트 (AI Agent)
- **키워드**: Tool Use, Function Calling, Agent Loop, Planning, Memory, Multi-Agent, AutoGPT, LangChain, CrewAI
- 에이전트란 — LLM이 "행동"하는 시스템
- Tool Use / Function Calling — 외부 도구 호출
- 에이전트 루프 (Agent Loop): 관찰 → 사고 → 행동 → 반복
- Planning — 복잡한 작업을 분해하는 능력
- Memory — 단기/장기 기억 관리
- Multi-Agent 시스템 — 여러 에이전트의 협업
- 프레임워크: LangChain, LangGraph, CrewAI, AutoGen
- Claude Code, Kilo Code가 에이전트인 이유

---

### Part 4: 생태계 — 당신이 매일 쓰는 도구의 이해

#### Chapter 12. MCP (Model Context Protocol)
- **키워드**: MCP Server, MCP Client, Tool, Resource, Prompt Template, Sequential Thinking, Transport Protocol
- MCP란 — AI 모델과 외부 세계를 잇는 표준 프로토콜
- 아키텍처: Host, Client, Server
- 핵심 개념: Tools, Resources, Prompts
- Sequential Thinking MCP — 구조화된 사고 지원
- Transport: stdio, HTTP/SSE
- MCP 서버 만들기 — 직접 도구를 확장하는 법
- MCP 생태계 현황과 주요 서버들

#### Chapter 13. AI 코딩 도구 생태계
- **키워드**: Claude Code, Cursor, GitHub Copilot, Kilo Code, Windsurf, Aider, Continue.dev, Code Completion, Code Generation
- AI 코딩 도구의 분류
  - 자동완성형: GitHub Copilot, Supermaven
  - 에이전트형: Claude Code, Kilo Code, Cursor, Windsurf
  - CLI형: Aider, OpenCode
- 내부 동작: 코드 컨텍스트 수집 → 프롬프트 구성 → LLM 호출 → 코드 적용
- 플러그인/확장 생태계: superclaude, opencode
- 코딩 도구가 코드를 이해하는 방법 — AST, LSP, Tree-sitter

#### Chapter 14. 모델 서빙과 인프라
- **키워드**: API, SDK, Inference Server, vLLM, TensorRT-LLM, Triton, GPU, TPU, CUDA, Rate Limiting, Streaming
- LLM API의 구조 — Request/Response, Streaming
- 모델 서빙 프레임워크: vLLM, TensorRT-LLM, Triton
- GPU와 LLM — CUDA, VRAM, 왜 GPU가 필요한가
- TPU (Google), Trainium (AWS) — GPU의 대안들
- Rate Limiting과 토큰 기반 과금 구조
- On-premise vs Cloud 배포
- Edge AI와 로컬 LLM (Ollama, llama.cpp)

---

### Part 5: 방법론 — AI 시대의 개발 방식

#### Chapter 15. AI 네이티브 개발 방법론
- **키워드**: Vibe Coding, Prompt-Driven Development, AI-Assisted TDD, Human-in-the-Loop, AI Pair Programming
- Vibe Coding — 자연어로 코딩하는 시대
- Prompt-Driven Development — 프롬프트가 곧 명세서
- AI-Assisted TDD — 테스트를 먼저 쓰고 AI에게 구현 맡기기
- Human-in-the-Loop — 사람의 검증이 필수인 이유
- AI Pair Programming — 짝 프로그래밍의 새로운 형태
- AI 코드 리뷰와 품질 관리
- 언제 AI를 쓰고 언제 직접 작성해야 하는가

#### Chapter 16. LLMOps와 평가
- **키워드**: LLMOps, Evaluation, Benchmark, MMLU, HumanEval, A/B Testing, Observability, Guardrails, Red Teaming
- LLMOps — LLM 기반 시스템의 운영
- 평가 방법: 벤치마크 (MMLU, HumanEval, GPQA)
- A/B 테스트와 온라인 평가
- Observability — LLM 호출 모니터링과 디버깅
- Guardrails — 출력 품질 보장 장치
- Red Teaming — 적대적 테스트로 취약점 발견
- 비용 최적화 — 토큰 사용량 관리, 모델 선택 전략

---

### Part 6: 프론티어 — 지금 일어나고 있는 일들

#### Chapter 17. 멀티모달 AI
- **키워드**: Vision-Language Model, Image Generation, Text-to-Image, Text-to-Video, Speech-to-Text, Multimodal Embedding
- Vision-Language Model — 이미지를 이해하는 LLM
- Text-to-Image: DALL-E, Midjourney, Stable Diffusion
- Diffusion Model — 이미지 생성의 원리
- Text-to-Video: Sora, Runway
- Speech-to-Text / Text-to-Speech
- 멀티모달 임베딩과 통합 표현 학습

#### Chapter 18. 추론 시간 컴퓨팅 (Test-Time Compute)
- **키워드**: Chain-of-Thought, Extended Thinking, o1, o3, DeepSeek-R1, Thinking Budget, Reasoning Model
- 추론 시간에 더 많이 "생각"하기
- Extended Thinking (Claude) — 사고 과정을 확장하는 방법
- OpenAI o1/o3 시리즈 — 추론 특화 모델
- DeepSeek-R1 — 오픈소스 추론 모델
- Thinking Budget — 생각에 얼마나 투자할 것인가
- 추론 스케일링 법칙 (Inference Scaling Law)

#### Chapter 19. 최신 트렌드와 미래 전망
- **키워드**: AGI, ASI, AI Safety, Alignment, Open Source AI, Mixture of Experts, State Space Model, Long Context, Agentic AI, AI Regulation
- Mixture of Experts (MoE) — 효율적인 대규모 모델
- State Space Model (Mamba) — Transformer의 대안?
- Long Context — 100K+ 토큰 시대
- Agentic AI — 자율 행동하는 AI의 미래
- AGI / ASI — 범용/초지능을 향한 논의
- AI Safety & Alignment — 안전하고 정렬된 AI
- AI 규제와 거버넌스 (EU AI Act, 각국 정책)
- 오픈소스 AI의 부상 (LLaMA, Mistral, DeepSeek)

---

### Part 7: 실전 도구 가이드 — 플러그인과 특화 도구

#### Chapter 20. AI CLI 플러그인과 확장 도구
- **키워드**: superclaude, opencode, Gemini CLI, aider, CLAUDE.md, MCP Server
- Claude Code 생태계와 CLAUDE.md 활용법
- superclaude — Claude Code 강화 플러그인
  - 설치 및 설정
  - 슬래시 커맨드 (/plan, /review, /refactor, /test)
  - Best Practices
- opencode — 범용 AI 코딩 CLI (다중 모델 지원)
- Gemini CLI와 플러그인 생태계
- aider — Git 중심 AI 페어 프로그래밍
- MCP 서버 활용하기 (Sequential Thinking, Filesystem, GitHub)
- CLI 도구 비교 및 선택 가이드

#### Chapter 21. 특화된 AI 도구 — Kilo Code, Bezi, Unity AI
- **키워드**: Kilo Code, Bezi, Unity Muse, Unity Sentis, ML-Agents
- Kilo Code — VS Code 네이티브 AI 코딩
  - 핵심 기능 (AI Chat, AI Edit, Agent Mode)
  - 설치 및 설정
  - Use Cases와 Best Practices
- Bezi — AI 기반 3D 디자인
  - 3D/XR 인터페이스 디자인
  - AI 기능 (텍스트→3D, 스타일 트랜스퍼)
  - Vision Pro, Quest 앱 프로토타이핑
- Unity AI — 게임 개발자를 위한 AI 도구
  - Unity Muse: 코드/텍스처/스프라이트 생성
  - Unity Sentis: 게임 내 AI 모델 실행
  - ML-Agents: 강화학습 에이전트 훈련
  - Use Cases (NPC AI, 동적 난이도, 프로시저럴 콘텐츠)

---

### 부록

#### Appendix A. 용어 사전 (Glossary)
- 자주 등장하는 약어와 용어 정리 (A-Z)

#### Appendix B. 추천 리소스
- 논문: Attention Is All You Need, GPT 시리즈, Constitutional AI 등
- 강의: Andrej Karpathy YouTube, fast.ai, Stanford CS224N
- 블로그: Anthropic Research, OpenAI Blog, Lilian Weng
- 도구: Hugging Face, LangChain, LlamaIndex

#### Appendix C. 실습 프로젝트
- 직접 해보며 이해하기
  1. Transformer 밑바닥부터 구현 (nanoGPT)
  2. RAG 파이프라인 구축
  3. MCP 서버 만들기
  4. AI 에이전트 직접 만들기
  5. LoRA로 모델 파인튜닝

---

## 학습 로드맵

```
[Part 1: 기초]          너는 여기서 시작
    │                   AI 역사, ML 기초, 딥러닝
    ▼
[Part 2: 핵심]          가장 중요한 파트
    │                   NLP, Transformer, LLM 내부
    ▼
[Part 3: 실전]          매일 쓰는 것의 원리
    │                   추론, 프롬프트, RAG, 에이전트
    ▼
[Part 4: 생태계]        도구의 이해
    │                   MCP, 코딩 도구, 인프라
    ▼
[Part 5: 방법론]        일하는 방식의 변화
    │                   AI 개발론, LLMOps
    ▼
[Part 6: 프론티어]      지금 벌어지고 있는 일
    │                   멀티모달, 추론 모델, 트렌드
    ▼
[Part 7: 실전 도구]     플러그인과 특화 도구
                        superclaude, Kilo Code, Bezi, Unity AI
```

---

*이 가이드는 Claude Code로 작성되었습니다.*
