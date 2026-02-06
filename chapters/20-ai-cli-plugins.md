# Chapter 20: AI CLI 플러그인과 확장 도구

> **"기본 도구만으로도 강력하지만, 플러그인을 더하면 날개를 단다."**
>
> Claude Code와 Gemini CLI는 그 자체로도 훌륭하지만,
> 커뮤니티에서 만든 플러그인과 확장 도구를 함께 쓰면 생산성이 배가됩니다.

---

## 핵심 키워드 요약

| 키워드 | 설명 |
|--------|------|
| **superclaude** | Claude Code의 시스템 프롬프트를 강화하는 CLAUDE.md 기반 확장 |
| **opencode** | 터미널 기반 AI 코딩 어시스턴트 (다중 모델 지원) |
| **Gemini CLI** | Google의 터미널 기반 Gemini AI 인터페이스 |
| **aider** | Git 통합이 뛰어난 AI pair programming 도구 |
| **CLAUDE.md** | 프로젝트별 AI 행동을 정의하는 설정 파일 |
| **MCP (Model Context Protocol)** | AI 모델과 외부 도구를 연결하는 표준 프로토콜 |

---

## 20.1 Claude Code 생태계

### 20.1.1 Claude Code 기본 구조

Claude Code는 Anthropic의 공식 CLI 도구로, 터미널에서 Claude와 대화하며 코딩 작업을 수행합니다.

```
Claude Code 아키텍처
┌─────────────────────────────────────────────────────────┐
│                    Claude Code CLI                       │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │ System      │  │ MCP         │  │ Hooks       │     │
│  │ Prompt      │  │ Servers     │  │ System      │     │
│  │ (CLAUDE.md) │  │             │  │             │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
├─────────────────────────────────────────────────────────┤
│                    Claude API                            │
└─────────────────────────────────────────────────────────┘
```

#### 핵심 설정 파일들

```bash
# 프로젝트 루트
./CLAUDE.md              # 프로젝트별 지침 (가장 중요!)

# 사용자 설정
~/.claude/
├── settings.json        # 전역 설정
├── claude_desktop_config.json  # MCP 서버 설정
└── hooks/              # 커스텀 훅 스크립트
```

---

## 20.2 superclaude — Claude Code 강화 플러그인

### 20.2.1 superclaude란?

**superclaude**는 Claude Code의 성능을 극대화하는 커뮤니티 플러그인입니다. 정교하게 설계된 CLAUDE.md 템플릿과 시스템 프롬프트를 제공하여 Claude가 더 체계적이고 일관된 방식으로 코딩 작업을 수행하도록 합니다.

### 20.2.2 주요 기능

```
superclaude 핵심 기능
├── 🎯 구조화된 작업 접근법
│   ├── 작업 분해 (Task Decomposition)
│   ├── 단계별 실행 계획
│   └── 체크포인트 기반 진행
│
├── 📋 향상된 프롬프트 패턴
│   ├── Chain-of-Thought 강제
│   ├── 코드 리뷰 체크리스트
│   └── 테스트 작성 가이드
│
├── 🔧 개발 워크플로우 통합
│   ├── Git 커밋 메시지 표준화
│   ├── PR 설명 자동 생성
│   └── 코드 문서화 템플릿
│
└── ⚡ 성능 최적화
    ├── 컨텍스트 관리 개선
    ├── 토큰 효율적 응답
    └── 반복 작업 자동화
```

### 20.2.3 설치 및 설정

```bash
# 1. superclaude 저장소 클론
git clone https://github.com/superclaude/superclaude.git

# 2. 프로젝트에 CLAUDE.md 복사
cp superclaude/templates/CLAUDE.md ./your-project/

# 3. 프로젝트에 맞게 커스터마이징
# CLAUDE.md 파일을 열고 프로젝트 정보 수정
```

### 20.2.4 CLAUDE.md 구조 예시

```markdown
# Project: My Awesome App

## Overview
이 프로젝트는 React + TypeScript로 만든 대시보드 애플리케이션입니다.

## Tech Stack
- Frontend: React 18, TypeScript, Tailwind CSS
- Backend: Node.js, Express, PostgreSQL
- Testing: Jest, React Testing Library

## Coding Standards
- 함수형 컴포넌트와 훅 사용
- 모든 컴포넌트에 TypeScript 타입 정의
- 컴포넌트당 하나의 파일
- 테스트 커버리지 80% 이상 유지

## File Structure
```
src/
├── components/    # 재사용 가능한 UI 컴포넌트
├── pages/         # 라우트별 페이지 컴포넌트
├── hooks/         # 커스텀 훅
├── utils/         # 유틸리티 함수
└── types/         # TypeScript 타입 정의
```

## Commands
- `npm run dev`: 개발 서버 실행
- `npm run test`: 테스트 실행
- `npm run build`: 프로덕션 빌드

## Important Notes
- API 키는 절대 하드코딩하지 않음
- 새 기능 추가 시 반드시 테스트 작성
- 커밋 전 lint 검사 통과 필수
```

### 20.2.5 superclaude 슬래시 커맨드

superclaude는 특수한 슬래시 커맨드를 제공합니다:

| 커맨드 | 설명 | 사용 예 |
|--------|------|---------|
| `/plan` | 작업 계획 수립 | `/plan 로그인 기능 구현` |
| `/review` | 코드 리뷰 수행 | `/review src/components/` |
| `/refactor` | 리팩토링 제안 | `/refactor --focus=performance` |
| `/test` | 테스트 코드 생성 | `/test src/utils/validator.ts` |
| `/doc` | 문서 생성 | `/doc --format=jsdoc` |
| `/debug` | 디버깅 모드 | `/debug "TypeError 발생"` |

### 20.2.6 Best Practices

```
superclaude 활용 Best Practices
─────────────────────────────────
1. CLAUDE.md는 항상 최신 상태로 유지
   → 프로젝트 구조가 바뀌면 즉시 업데이트

2. 작업 범위를 명확히 지정
   → "전체 리팩토링" 보다 "UserService 클래스 리팩토링"

3. 컨텍스트 오버로드 방지
   → 한 번에 너무 많은 파일을 다루지 않음

4. 슬래시 커맨드 적극 활용
   → /plan으로 시작하면 더 체계적인 결과

5. 피드백 루프 활용
   → 결과가 마음에 들지 않으면 구체적으로 수정 요청
```

---

## 20.3 opencode — 범용 AI 코딩 CLI

### 20.3.1 opencode란?

**opencode**는 여러 AI 모델(OpenAI, Anthropic, Ollama 등)을 지원하는 터미널 기반 AI 코딩 어시스턴트입니다. Claude Code와 유사한 인터페이스를 제공하면서도 모델을 자유롭게 선택할 수 있습니다.

### 20.3.2 주요 특징

```
opencode 특징
├── 🔄 다중 모델 지원
│   ├── OpenAI (GPT-4, GPT-4o)
│   ├── Anthropic (Claude)
│   ├── Google (Gemini)
│   ├── Ollama (로컬 모델)
│   └── 기타 OpenAI 호환 API
│
├── 💻 터미널 네이티브
│   ├── TUI (Terminal User Interface)
│   ├── Vim 키바인딩 지원
│   └── 세션 관리
│
├── 📁 프로젝트 인식
│   ├── Git 통합
│   ├── 파일 트리 탐색
│   └── 코드 검색
│
└── 🔌 확장성
    ├── 커스텀 프롬프트
    ├── 플러그인 시스템
    └── 스크립팅 지원
```

### 20.3.3 설치

```bash
# Go로 설치
go install github.com/opencode-ai/opencode@latest

# 또는 brew (macOS)
brew install opencode

# 또는 직접 바이너리 다운로드
curl -fsSL https://opencode.ai/install.sh | sh
```

### 20.3.4 설정

```yaml
# ~/.opencode/config.yaml
default_model: claude-3-5-sonnet

models:
  claude-3-5-sonnet:
    provider: anthropic
    api_key: ${ANTHROPIC_API_KEY}

  gpt-4o:
    provider: openai
    api_key: ${OPENAI_API_KEY}

  local-llama:
    provider: ollama
    model: llama3.2
    base_url: http://localhost:11434

editor: nvim
theme: dracula
```

### 20.3.5 사용법

```bash
# 기본 실행
opencode

# 특정 모델로 실행
opencode --model gpt-4o

# 프로젝트 디렉토리 지정
opencode --dir /path/to/project

# 비대화형 모드 (스크립트용)
opencode --non-interactive "이 함수에 테스트 작성해줘" < src/utils.ts
```

### 20.3.6 opencode vs Claude Code 비교

| 기능 | Claude Code | opencode |
|------|------------|----------|
| 모델 지원 | Claude만 | 다중 모델 |
| MCP 지원 | 네이티브 | 제한적 |
| 공식 지원 | Anthropic 공식 | 커뮤니티 |
| 로컬 모델 | 불가 | Ollama 지원 |
| 가격 | Claude API 비용 | 선택한 모델에 따름 |
| 확장성 | CLAUDE.md, 훅 | 플러그인, 스크립트 |

---

## 20.4 Gemini CLI와 플러그인

### 20.4.1 Gemini CLI 소개

**Gemini CLI**는 Google의 Gemini 모델을 터미널에서 사용할 수 있게 해주는 도구입니다. 2024년 말부터 본격적으로 지원되기 시작했습니다.

### 20.4.2 설치 및 설정

```bash
# npm으로 설치
npm install -g @google/gemini-cli

# 또는 직접 설치
curl -fsSL https://gemini.google.com/cli/install.sh | sh

# API 키 설정
export GEMINI_API_KEY="your-api-key"

# 또는 설정 파일
echo "api_key: your-api-key" > ~/.gemini/config.yaml
```

### 20.4.3 기본 사용법

```bash
# 대화 시작
gemini chat

# 코드 생성
gemini code "Python으로 퀵소트 구현"

# 파일 분석
gemini analyze src/main.py

# 이미지 분석 (Gemini Pro Vision)
gemini vision image.png "이 다이어그램을 설명해줘"
```

### 20.4.4 Gemini CLI 플러그인 생태계

```
Gemini CLI 플러그인
├── gemini-code-review
│   └── PR/MR 코드 리뷰 자동화
│
├── gemini-docs
│   └── 코드 문서 자동 생성
│
├── gemini-translate
│   └── 코드 주석 다국어 번역
│
├── gemini-sql
│   └── 자연어 → SQL 변환
│
└── gemini-shell
    └── 자연어로 셸 명령 생성
```

### 20.4.5 플러그인 설치 및 사용

```bash
# 플러그인 설치
gemini plugin install gemini-code-review

# 플러그인 목록 확인
gemini plugin list

# 플러그인 사용
gemini code-review ./src --output=report.md
```

---

## 20.5 aider — Git 중심 AI 페어 프로그래밍

### 20.5.1 aider란?

**aider**는 Git과 깊이 통합된 AI 페어 프로그래밍 도구입니다. 코드 변경사항을 자동으로 Git 커밋하고, 여러 파일을 동시에 수정할 수 있습니다.

### 20.5.2 핵심 특징

```
aider의 강점
├── 🔗 Git 네이티브 통합
│   ├── 변경사항 자동 커밋
│   ├── 커밋 메시지 자동 생성
│   └── diff 기반 수정 적용
│
├── 📁 다중 파일 편집
│   ├── 여러 파일 동시 수정
│   ├── 파일 간 참조 이해
│   └── 리팩토링 지원
│
├── 🤖 다중 모델 지원
│   ├── GPT-4, Claude, Gemini
│   ├── Ollama 로컬 모델
│   └── DeepSeek, Mistral
│
└── 📊 코드 맵 기능
    ├── 전체 코드베이스 인덱싱
    ├── 관련 파일 자동 탐지
    └── 컨텍스트 최적화
```

### 20.5.3 설치 및 설정

```bash
# pip으로 설치
pip install aider-chat

# 또는 pipx (권장)
pipx install aider-chat

# API 키 설정
export ANTHROPIC_API_KEY="your-key"
# 또는
export OPENAI_API_KEY="your-key"
```

### 20.5.4 기본 사용법

```bash
# 현재 디렉토리에서 시작
aider

# 특정 파일들로 시작
aider src/main.py src/utils.py

# 모델 지정
aider --model claude-3-5-sonnet

# 자동 커밋 비활성화
aider --no-auto-commits

# 읽기 전용 파일 추가 (컨텍스트용)
aider --read README.md src/types.ts
```

### 20.5.5 aider 세션 예시

```
$ aider src/api/users.py src/api/auth.py

Aider v0.50.0
Model: claude-3-5-sonnet
Git repo: .git with 47 files
Added src/api/users.py to the chat.
Added src/api/auth.py to the chat.

> 로그인 API에 rate limiting 추가해줘

I'll add rate limiting to the login endpoint using a sliding window algorithm.

src/api/auth.py
<<<<<<< SEARCH
@router.post("/login")
async def login(credentials: LoginRequest):
    user = await authenticate(credentials)
    return create_token(user)
=======
from ..utils.rate_limit import RateLimiter

rate_limiter = RateLimiter(max_requests=5, window_seconds=60)

@router.post("/login")
async def login(credentials: LoginRequest, request: Request):
    client_ip = request.client.host
    if not rate_limiter.is_allowed(client_ip):
        raise HTTPException(429, "Too many login attempts")
    user = await authenticate(credentials)
    return create_token(user)
>>>>>>> REPLACE

Commit message: feat: add rate limiting to login endpoint

> y

Applied edit to src/api/auth.py
Commit abc1234 feat: add rate limiting to login endpoint
```

### 20.5.6 aider 고급 기능

```bash
# 전체 코드베이스를 맵으로 사용
aider --map-tokens 2048

# 아키텍트 모드 (계획 수립 + 구현)
aider --architect

# 음성 입력
aider --voice

# 웹 UI 모드
aider --browser
```

---

## 20.6 CLI 도구 비교 및 선택 가이드

### 20.6.1 종합 비교표

| 기능 | Claude Code | opencode | Gemini CLI | aider |
|------|-------------|----------|------------|-------|
| **주요 모델** | Claude | 다중 | Gemini | 다중 |
| **MCP 지원** | ✅ 네이티브 | ❌ | ❌ | ❌ |
| **Git 통합** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| **다중 파일** | ✅ | ✅ | ✅ | ✅ (최고) |
| **로컬 모델** | ❌ | ✅ | ❌ | ✅ |
| **확장성** | CLAUDE.md | 플러그인 | 플러그인 | 설정 |
| **가격** | Claude API | 선택 | Gemini API | 선택 |
| **러닝커브** | 낮음 | 중간 | 낮음 | 중간 |

### 20.6.2 상황별 추천

```
어떤 도구를 선택할까?
─────────────────────

🏢 회사에서 Claude를 주로 쓴다면
   → Claude Code + superclaude

💰 비용을 최소화하고 싶다면
   → opencode + Ollama 로컬 모델

🔄 Git 워크플로우가 중요하다면
   → aider (자동 커밋이 핵심)

🎨 멀티모달 작업이 많다면
   → Gemini CLI (이미지 분석 강점)

🧪 여러 모델을 비교 테스트하고 싶다면
   → opencode 또는 aider (다중 모델 지원)
```

---

## 20.7 MCP 서버 활용하기

### 20.7.1 Claude Code와 MCP

Claude Code는 MCP를 네이티브로 지원하여 다양한 외부 도구와 연동할 수 있습니다.

```json
// ~/.claude/claude_desktop_config.json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-sequential-thinking"]
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-filesystem", "/path/to/allowed/dir"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-github"],
      "env": {
        "GITHUB_TOKEN": "your-token"
      }
    }
  }
}
```

### 20.7.2 유용한 MCP 서버들

| MCP 서버 | 용도 | 설치 |
|----------|------|------|
| sequential-thinking | 구조화된 추론 | `@anthropic/mcp-sequential-thinking` |
| filesystem | 파일 시스템 접근 | `@anthropic/mcp-filesystem` |
| github | GitHub API 연동 | `@anthropic/mcp-github` |
| postgres | PostgreSQL 쿼리 | `@anthropic/mcp-postgres` |
| puppeteer | 웹 브라우징 | `@anthropic/mcp-puppeteer` |
| memory | 영구 메모리 | `@anthropic/mcp-memory` |

### 20.7.3 Sequential Thinking MCP 활용

Sequential Thinking은 복잡한 문제를 단계별로 분해하여 해결하는 MCP 서버입니다.

```
Sequential Thinking 작동 방식
────────────────────────────
1. 문제 분석 (Problem Analysis)
   └── 주어진 문제의 핵심 요소 파악

2. 단계 분해 (Step Decomposition)
   └── 해결에 필요한 단계들 나열

3. 순차 실행 (Sequential Execution)
   └── 각 단계를 순서대로 실행

4. 검증 (Verification)
   └── 각 단계의 결과 검증

5. 종합 (Synthesis)
   └── 최종 답변 도출
```

---

## 20.8 실전 워크플로우 예시

### 20.8.1 새 기능 개발 워크플로우

```bash
# 1. Claude Code + superclaude로 시작
cd my-project
claude

> /plan 사용자 프로필 페이지 구현

# 2. 계획 검토 후 구현 시작
> 컴포넌트 구조부터 만들어줘

# 3. 테스트 작성
> /test src/components/UserProfile.tsx

# 4. 코드 리뷰
> /review --focus=security

# 5. 커밋 (Claude Code가 자동 생성)
> 지금까지 변경사항 커밋해줘
```

### 20.8.2 버그 수정 워크플로우 (aider 활용)

```bash
# 1. 관련 파일들로 aider 시작
aider src/api/payment.py src/services/stripe.py tests/test_payment.py

> 결제 실패 시 재시도 로직이 제대로 동작하지 않아.
> 에러 로그를 보면 RetryError가 발생하고 있어.

# 2. aider가 코드 분석 후 수정안 제시
# 3. 변경사항 검토 후 승인
> y

# 4. 자동으로 Git 커밋됨
```

### 20.8.3 코드 리뷰 자동화 (Gemini CLI)

```bash
# PR 생성 전 자동 리뷰
gemini code-review ./src \
  --check security \
  --check performance \
  --check best-practices \
  --output pr-review.md

# 결과를 PR 설명에 포함
gh pr create --body-file pr-review.md
```

---

## 20.9 Best Practices 종합

### 20.9.1 도구 조합 전략

```
권장 도구 조합
─────────────
🥇 메인: Claude Code + superclaude
   └── 일상적인 개발 작업의 90%

🥈 서브: aider
   └── 대규모 리팩토링, 다중 파일 수정

🥉 보조: Gemini CLI
   └── 이미지/다이어그램 분석, 문서 생성

🏅 로컬: opencode + Ollama
   └── 오프라인 작업, 민감한 코드
```

### 20.9.2 공통 Best Practices

1. **CLAUDE.md / 설정 파일 관리**
   - 프로젝트마다 맞춤 설정 유지
   - 팀원과 설정 파일 공유 (Git 추적)
   - 정기적으로 업데이트

2. **컨텍스트 최적화**
   - 필요한 파일만 추가 (토큰 절약)
   - 읽기 전용 파일과 편집 파일 구분
   - 대규모 파일은 필요한 부분만

3. **버전 관리 통합**
   - 자동 커밋 기능 적극 활용
   - 의미 있는 단위로 커밋
   - PR 전 AI 리뷰 실행

4. **보안 고려**
   - API 키, 시크릿 노출 주의
   - 민감한 코드는 로컬 모델 고려
   - .gitignore에 설정 파일 추가 검토

---

## 요약

```
AI CLI 도구 생태계 정리
─────────────────────────
┌─────────────────────────────────────────────────────┐
│                    사용자 (개발자)                    │
├─────────────────────────────────────────────────────┤
│   CLI 도구 선택                                      │
│   ┌─────────┬─────────┬─────────┬─────────┐        │
│   │ Claude  │opencode │ Gemini  │  aider  │        │
│   │  Code   │         │   CLI   │         │        │
│   └────┬────┴────┬────┴────┬────┴────┬────┘        │
│        │         │         │         │             │
├────────┼─────────┼─────────┼─────────┼─────────────┤
│   확장 │         │         │         │             │
│   ┌────┴────┐    │         │         │             │
│   │super-   │    │         │         │             │
│   │claude   │    │         │         │             │
│   │MCP      │    │         │         │             │
│   └─────────┘    │         │         │             │
├──────────────────┴─────────┴─────────┴─────────────┤
│                    AI 모델 API                       │
│   ┌─────────┬─────────┬─────────┬─────────┐        │
│   │ Claude  │  GPT-4  │ Gemini  │ Ollama  │        │
│   └─────────┴─────────┴─────────┴─────────┘        │
└─────────────────────────────────────────────────────┘
```

**핵심 메시지**: 하나의 도구에 올인하기보다, 상황에 맞는 도구를 선택하고 조합하는 것이 현명합니다. Claude Code를 메인으로 쓰면서 특정 작업에는 다른 도구를 활용하세요.

---

*다음 장에서는 Kilo Code, Bezi, Unity AI 등 특화된 AI 도구들을 살펴봅니다.*
