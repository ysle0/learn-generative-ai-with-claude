# Chapter 22: Spec-Driven Development와 Agent-First IDE

> **"Vibe Coding의 시대는 끝났다. 이제는 명세가 코드를 만든다."**
>
> AI 에이전트가 코드를 작성하는 시대, 우리는 어떻게 일해야 할까요?
> 이 장에서는 Spec-Driven Development(SDD)와 Agent-First Development의
> 개념, 도구, 그리고 실전 워크플로우를 다룹니다.

---

## 핵심 키워드 요약

| 키워드 | 설명 |
|--------|------|
| **Spec-Driven Development (SDD)** | 명세(Specification)를 먼저 작성하고, AI가 그에 맞춰 코드를 생성하는 개발 방법론 |
| **Agent-First Development** | AI 에이전트를 개발의 중심에 두고 인간은 검토/승인하는 개발 패러다임 |
| **GitHub Spec Kit** | GitHub의 오픈소스 SDD 툴킷 (spec → plan → tasks 워크플로우) |
| **Cursor Rules** | Cursor IDE의 AI 행동 규칙 정의 시스템 (.cursor/rules) |
| **Google Antigravity** | Google의 Agent-First IDE (Windsurf 팀 합류 후 개발) |
| **Artifacts** | 에이전트 작업의 검증 가능한 산출물 (계획, 스크린샷, 녹화 등) |

---

## 22.1 Vibe Coding의 한계

### 22.1.1 Vibe Coding이란?

**Vibe Coding**은 AI 코딩 도구에게 대략적인 의도만 전달하고 결과를 받아보는 방식입니다.

```
Vibe Coding 예시
────────────────
개발자: "로그인 기능 만들어줘"
AI: [코드 생성]
개발자: (대충 훑어보고) "음... 되는 것 같네" → 커밋

문제점:
- 코드가 "보기에는" 맞아 보이지만 실제로는 작동하지 않음
- 엣지 케이스 누락
- 보안 취약점 존재
- 기존 코드베이스와 일관성 없음
```

### 22.1.2 왜 Vibe Coding이 실패하는가

```
Vibe Coding 실패 원인
─────────────────────────────────────────────

┌─────────────────────────────────────────────┐
│              개발자의 의도                   │
│  "로그인 기능을 만들어줘"                    │
└─────────────────────────────────────────────┘
                    ▼
           [모호한 요구사항]
                    ▼
┌─────────────────────────────────────────────┐
│              AI의 해석                       │
│  "어떤 로그인? OAuth? 세션? JWT?            │
│   에러 처리는? 비밀번호 정책은?             │
│   기존 시스템과의 통합은?"                   │
└─────────────────────────────────────────────┘
                    ▼
              [추측으로 구현]
                    ▼
┌─────────────────────────────────────────────┐
│              결과물                          │
│  • 보기엔 그럴듯하지만 요구사항 불일치       │
│  • 기존 코드 스타일과 불일치                 │
│  • 누락된 엣지 케이스                        │
└─────────────────────────────────────────────┘
```

**핵심 문제**: AI는 코딩 능력은 뛰어나지만, 불명확한 지시에는 추측할 수밖에 없습니다.

---

## 22.2 Spec-Driven Development (SDD)

### 22.2.1 SDD란?

**Spec-Driven Development**는 코드를 작성하기 전에 **명세(Specification)**를 먼저 작성하고, 이를 AI 에이전트가 구현하도록 하는 개발 방법론입니다.

```
SDD vs Vibe Coding 비교
────────────────────────────────────────────────

Vibe Coding:
  "로그인 만들어줘" → [AI 추측] → 코드 → 수정 → 수정 → ...

Spec-Driven Development:
  명세 작성 → 계획 수립 → 태스크 분해 → 구현 → 검증
     ↑          ↑          ↑           ↑       ↑
   인간       AI+인간    AI+인간      AI     인간
```

### 22.2.2 SDD의 4단계 워크플로우

```
Spec-Driven Development 4단계
─────────────────────────────────────────────────

Phase 1: SPECIFY (명세)
├── 목표와 사용자 여정 정의
├── 기능 요구사항 상세화
├── 제약조건 명시
└── AI가 명세 초안 작성 → 인간이 피드백

Phase 2: PLAN (계획)
├── 아키텍처 결정
├── 기술 스택 선언
├── 의존성 정리
└── AI가 기술 계획 제안 → 인간이 승인

Phase 3: TASKS (태스크)
├── 작업을 작은 단위로 분해
├── 각 태스크에 검증 기준 포함
├── 순서와 의존성 정의
└── AI가 태스크 목록 생성 → 인간이 검토

Phase 4: IMPLEMENT (구현)
├── 태스크별로 코드 생성
├── 각 태스크 완료 시 검증
├── 테스트 통과 확인
└── AI가 구현 → 인간이 리뷰/승인
```

### 22.2.3 명세의 구성 요소

```markdown
# 기능 명세서 (예시)

## 1. 개요
사용자 인증 시스템 구현

## 2. 사용자 스토리
- 사용자로서 이메일/비밀번호로 로그인하고 싶다
- 사용자로서 Google OAuth로 로그인하고 싶다
- 사용자로서 비밀번호를 분실했을 때 재설정하고 싶다

## 3. 기능 요구사항
### 3.1 이메일/비밀번호 로그인
- 이메일 형식 검증
- 비밀번호 최소 8자, 대소문자+숫자+특수문자 포함
- 5회 실패 시 15분 잠금
- JWT 토큰 발급 (1시간 만료)
- Refresh 토큰 발급 (7일 만료)

### 3.2 OAuth 로그인
- Google OAuth 2.0 지원
- 신규 사용자는 자동 가입
- 기존 이메일과 연동

## 4. 기술 제약
- 기존 Express.js 서버에 통합
- PostgreSQL 사용자 테이블 활용
- 기존 세션 시스템과 병행 운영

## 5. 보안 요구사항
- 비밀번호 bcrypt 해싱 (cost factor 12)
- HTTPS만 허용
- CSRF 토큰 검증
- Rate limiting (IP당 분당 10회)

## 6. 에러 시나리오
- 잘못된 이메일/비밀번호: 401 + 일반적 메시지
- 계정 잠금: 429 + 남은 시간 안내
- OAuth 실패: 리다이렉트 + 에러 메시지
```

---

## 22.3 GitHub Spec Kit

### 22.3.1 Spec Kit 소개

[**GitHub Spec Kit**](https://github.com/github/spec-kit)은 GitHub에서 2025년 9월 공개한 오픈소스 SDD 툴킷입니다. 다양한 AI 코딩 도구(Claude Code, GitHub Copilot, Cursor, Gemini CLI 등)와 함께 사용할 수 있습니다.

```
Spec Kit 지원 AI 도구
─────────────────────
• GitHub Copilot (Agent Mode)
• Claude Code
• Cursor Agent
• Gemini CLI
• Windsurf
• Kilo Code
• OpenCode
• Qwen, Codex, Roo, AMP 등
```

### 22.3.2 설치 및 초기화

```bash
# 설치 (Python 3.11+ 필요)
pip install spec-kit

# 또는 uvx 사용 (권장)
uvx spec-kit

# 새 프로젝트 초기화
spec-kit init --agent claude

# 또는 대화형 모드
spec-kit init
# ? Select your AI agent: claude
# ? Project name: my-awesome-app
```

### 22.3.3 생성되는 파일 구조

```
my-awesome-app/
├── .spec/
│   ├── spec.md           # 기능 명세서
│   ├── plan.md           # 기술 계획
│   └── tasks/            # 태스크 파일들
│       ├── 001-setup.md
│       ├── 002-auth.md
│       └── ...
├── AGENTS.md             # AI 에이전트 가이드라인
└── ... (프로젝트 파일들)
```

### 22.3.4 Spec Kit 워크플로우

```bash
# 1. 명세 작성 시작
spec-kit specify "사용자 인증 시스템 with OAuth"
# → .spec/spec.md 생성 (AI가 초안 작성)
# → 개발자가 검토/수정

# 2. 기술 계획 수립
spec-kit plan
# → .spec/plan.md 생성
# → 아키텍처, 스택, 의존성 정의

# 3. 태스크 분해
spec-kit tasks
# → .spec/tasks/ 디렉토리에 태스크 파일 생성
# → 각 태스크는 독립적으로 검증 가능

# 4. 구현
spec-kit implement 001
# → 첫 번째 태스크 구현
# → AI 에이전트가 코드 생성 + 테스트

# 5. 검증
spec-kit verify 001
# → 태스크 완료 검증
# → 테스트 통과, 명세 충족 확인
```

### 22.3.5 Spec Kit의 장점과 한계

```
장점 ✅
─────
• 재현 가능한 개발 프로세스
• AI와 인간의 명확한 역할 분리
• 검증 가능한 중간 산출물
• 여러 AI 도구와 호환

한계 ⚠️
─────
• 초기화 후 AI 도구 변경 어려움
• 기존 프로젝트보다 신규 프로젝트에 적합
• Python 3.11+ 필요
• 아직 실험적 단계 (v0.x)
```

---

## 22.4 Agent-First Development

### 22.4.1 Agent-First란?

**Agent-First Development**는 AI 에이전트를 개발의 중심에 두는 패러다임입니다. 개발자는 직접 코드를 작성하기보다, 에이전트에게 작업을 위임하고 결과를 검토/승인하는 역할을 합니다.

```
개발자 역할의 변화
─────────────────────────────────────────────

전통적 개발:
  개발자 = 코드 작성자 (Typist)
  도구 = 편집기, 컴파일러

AI-Assisted 개발 (현재):
  개발자 = 코드 작성자 + AI 활용
  AI = 자동완성, 제안, 리뷰

Agent-First 개발 (미래):
  개발자 = 아키텍트, 검토자 (Architect)
  AI = 자율적 구현자
  개발자: "무엇을" 정의 → AI: "어떻게" 구현
```

### 22.4.2 Agent-First의 핵심 원칙

```
Agent-First 5대 원칙
─────────────────────

1. 명세 우선 (Spec First)
   - 코드 전에 명세를 작성
   - 명세가 곧 계약(Contract)

2. 검증 가능한 산출물 (Verifiable Artifacts)
   - 에이전트는 코드만 내놓지 않음
   - 계획, 테스트, 문서를 함께 생성

3. 인간의 게이트키핑 (Human Gatekeeping)
   - 핵심 결정은 인간이
   - 에이전트는 제안, 인간은 승인

4. 점진적 위임 (Progressive Delegation)
   - 작은 작업부터 위임
   - 신뢰 구축 후 범위 확대

5. 실행 투명성 (Execution Transparency)
   - 에이전트의 모든 행동 로깅
   - 왜 그렇게 했는지 설명 요구
```

---

## 22.5 Cursor IDE와 Rules 시스템

### 22.5.1 Cursor Rules란?

[**Cursor**](https://cursor.com)는 AI-First IDE로, `.cursor/rules` 파일을 통해 AI 에이전트의 행동을 정밀하게 제어할 수 있습니다.

```
Cursor Rules 시스템
───────────────────────────────────────────

.cursor/
└── rules
    ├── global.md          # 전역 규칙 (항상 적용)
    ├── typescript.md      # TypeScript 파일에만 적용
    ├── testing.md         # 테스트 관련 규칙
    └── security.md        # 보안 관련 규칙
```

### 22.5.2 Rules 파일 작성법

```markdown
<!-- .cursor/rules/global.md -->
---
description: "전역 코딩 규칙"
alwaysApply: true
---

## 프로젝트 개요
이 프로젝트는 Next.js 14 App Router를 사용하는 SaaS 대시보드입니다.

## 코딩 스타일
- TypeScript 사용 필수 (any 금지)
- 함수형 컴포넌트만 사용
- 상태 관리: Zustand 사용
- 스타일링: Tailwind CSS

## 금지 사항
- console.log 커밋 금지 (디버깅용만 허용)
- 하드코딩된 API 키 금지
- 인라인 스타일 금지

## 테스트
- 새 기능에는 반드시 테스트 작성
- 테스트 파일: *.test.ts 또는 *.spec.ts
- 최소 커버리지: 80%

## 파일 구조
```
src/
├── app/           # Next.js App Router 페이지
├── components/    # 재사용 컴포넌트
├── lib/           # 유틸리티, 헬퍼
├── hooks/         # 커스텀 훅
└── types/         # TypeScript 타입
```
```

### 22.5.3 조건부 Rules

```markdown
<!-- .cursor/rules/api-routes.md -->
---
description: "API 라우트 작성 규칙"
globs: ["**/api/**/*.ts"]
alwaysApply: false
---

## API 라우트 규칙

### 구조
```typescript
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  try {
    // 비즈니스 로직
    return NextResponse.json({ data });
  } catch (error) {
    return NextResponse.json(
      { error: 'Internal Server Error' },
      { status: 500 }
    );
  }
}
```

### 필수 사항
- 모든 라우트에 try-catch
- 적절한 HTTP 상태 코드 반환
- 에러 메시지는 사용자에게 안전하게
- Rate limiting 미들웨어 적용
```

### 22.5.4 AGENTS.md vs .cursor/rules

```
파일별 용도 비교
─────────────────────────────────────────────

AGENTS.md (단일 파일)
├── 용도: 간단한 프로젝트, 범용 가이드
├── 구조: 하나의 마크다운 파일
├── 장점: 설정 간편, 모든 AI 도구 호환
└── 예시: 빌드 명령, 코딩 스타일, 금지 사항

.cursor/rules (구조화된 규칙)
├── 용도: 복잡한 프로젝트, 세밀한 제어
├── 구조: 여러 파일, 조건부 적용
├── 장점: 컨텍스트별 규칙, 재사용 가능
└── 예시: 파일 타입별 규칙, 디렉토리별 규칙
```

### 22.5.5 Cursor Agent Mode

```
Cursor의 두 가지 모드
─────────────────────

Ask Mode (질문 모드)
├── 코드 설명, 분석, 제안
├── 파일 수정 없음
├── 계획 수립에 적합
└── "이 코드가 뭘 하는지 설명해줘"

Agent Mode (에이전트 모드)
├── 자율적 코드 작성/수정
├── 파일 생성, 삭제, 이동
├── 터미널 명령 실행
├── 여러 파일 동시 수정
└── "로그인 기능을 구현해줘"

베스트 프랙티스:
1. Ask Mode로 계획 수립
2. Agent Mode로 구현
3. 결과 검토 후 승인/수정
```

---

## 22.6 Google Antigravity

### 22.6.1 Antigravity 소개

[**Google Antigravity**](https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/)는 2025년 11월 Gemini 3와 함께 발표된 Google의 Agent-First IDE입니다. Windsurf 팀을 인수하여 개발했습니다.

```
Antigravity 핵심 특징
─────────────────────────────────────────────

🚀 Agent-First 설계
├── 에이전트가 개발의 중심
├── 개발자는 "아키텍트" 역할
└── 비동기 작업 실행 가능

🖥️ 두 가지 뷰
├── Editor View: 일반 IDE (Cursor와 유사)
└── Manager View: 에이전트 오케스트레이션

🔍 Artifacts (산출물)
├── 구현 계획서
├── 태스크 목록
├── 스크린샷
└── 브라우저 녹화

🤖 모델 지원
├── Gemini 3 Pro/Flash/Deep Think
├── Claude Sonnet 4.5 / Opus 4.5
└── GPT-OSS-120B (오픈소스)
```

### 22.6.2 Editor View vs Manager View

```
Antigravity의 두 인터페이스
─────────────────────────────────────────────

┌─────────────────────────────────────────────┐
│                Editor View                   │
├─────────────────────────────────────────────┤
│  [코드 에디터]          [에이전트 사이드바]  │
│  ┌──────────────┐      ┌───────────────┐    │
│  │ function x() │      │ 💬 무엇을      │    │
│  │   ...        │      │    도와드릴까요?│    │
│  │              │      │               │    │
│  │              │      │ [채팅 기록]    │    │
│  └──────────────┘      └───────────────┘    │
│                                             │
│  → 일반적인 AI IDE와 유사                   │
│  → 대화형 코딩, 빠른 수정                   │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│               Manager View                   │
├─────────────────────────────────────────────┤
│  [워크스페이스 1]  [워크스페이스 2]  [+추가] │
│  ┌─────────────┐  ┌─────────────┐           │
│  │ Agent A     │  │ Agent B     │           │
│  │ ■ 구현 중   │  │ ✓ 완료     │           │
│  │ 진행: 45%   │  │ 리뷰 대기  │           │
│  │ [Artifacts] │  │ [Artifacts] │           │
│  └─────────────┘  └─────────────┘           │
│                                             │
│  → 여러 에이전트 동시 관리                  │
│  → 비동기 작업 모니터링                     │
│  → 프로젝트 오케스트레이션                  │
└─────────────────────────────────────────────┘
```

### 22.6.3 Artifacts 시스템

Antigravity의 에이전트는 코드만 생성하지 않고, **검증 가능한 산출물(Artifacts)**을 함께 제공합니다.

```
Artifacts 종류
───────────────
1. 구현 계획 (Implementation Plan)
   - 무엇을 어떤 순서로 할 것인지

2. 태스크 체크리스트
   - 각 태스크의 완료 상태

3. 코드 변경 요약
   - 수정된 파일, 추가된 라인

4. 스크린샷
   - UI 변경 전/후 비교

5. 브라우저 녹화
   - E2E 테스트 실행 영상

6. 의사결정 기록
   - 왜 그렇게 구현했는지 설명
```

### 22.6.4 비동기 에이전트 실행

```
비동기 작업 예시
────────────────────────────────────────────

개발자: "이 3가지 기능을 구현해줘:
        1. 사용자 프로필 페이지
        2. 설정 페이지
        3. 알림 시스템"

Antigravity Manager:
┌────────────────────────────────────────────┐
│  🤖 Agent 1: 프로필 페이지                 │
│     상태: ■■■■□□□□ 구현 중 (50%)          │
│     예상 완료: 15분                        │
│                                            │
│  🤖 Agent 2: 설정 페이지                   │
│     상태: ■■■■■■□□ 구현 중 (75%)          │
│     예상 완료: 8분                         │
│                                            │
│  🤖 Agent 3: 알림 시스템                   │
│     상태: ■■□□□□□□ 시작 (25%)             │
│     예상 완료: 25분                        │
└────────────────────────────────────────────┘

→ 개발자는 다른 작업을 하다가 완료 알림 받음
→ 각 작업 결과를 리뷰하고 승인/수정
```

---

## 22.7 Kilo Code의 Spec 기능

### 22.7.1 Kilo Code에서의 SDD

Kilo Code도 GitHub Spec Kit과 유사한 spec-driven 워크플로우를 지원합니다.

```
Kilo Code SDD 워크플로우
─────────────────────────

1. 프로젝트 설정
   - .kilocode/config.yaml 설정
   - 에이전트 동작 규칙 정의

2. 명세 작성
   - /spec 명령으로 명세 초안 생성
   - 개발자가 검토/보완

3. 계획 수립
   - /plan 명령으로 구현 계획 생성
   - 아키텍처, 파일 구조 결정

4. 구현
   - Agent Mode로 태스크별 구현
   - 각 단계에서 검증

5. 리뷰
   - /review로 전체 코드 리뷰
   - 누락/오류 체크
```

### 22.7.2 Kilo Code 설정 예시

```yaml
# .kilocode/config.yaml
project:
  name: "my-saas-app"
  type: "web-application"

agent:
  model: "claude-3-5-sonnet"
  auto_approve: false

rules:
  - file: "*.tsx"
    instructions: |
      - 함수형 컴포넌트 사용
      - Props는 인터페이스로 정의
      - 컴포넌트 이름은 PascalCase

  - file: "*.test.ts"
    instructions: |
      - Jest + Testing Library 사용
      - describe/it 구조
      - 각 케이스에 명확한 설명

spec:
  require_approval: true
  template: "detailed"
```

---

## 22.8 도구 비교

### 22.8.1 종합 비교표

| 기능 | GitHub Spec Kit | Cursor Rules | Antigravity | Kilo Code |
|------|-----------------|--------------|-------------|-----------|
| **유형** | 프로세스 툴킷 | IDE 규칙 | Agent-First IDE | VS Code 확장 |
| **SDD 지원** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **에이전트 자율성** | 중간 | 높음 | 매우 높음 | 높음 |
| **다중 에이전트** | ❌ | ❌ | ✅ | ❌ |
| **비동기 작업** | ❌ | ❌ | ✅ | ❌ |
| **Artifacts** | ✅ (태스크) | ❌ | ✅ (풍부) | ❌ |
| **가격** | 무료 (OSS) | Cursor 구독 | 프리뷰 무료 | 무료+유료 |
| **모델 지원** | 다중 | 다중 | 다중 | 다중 |

### 22.8.2 상황별 선택 가이드

```
어떤 도구를 선택할까?
─────────────────────────────────────────────

🆕 신규 프로젝트, 명세 중심 개발하고 싶다면
   → GitHub Spec Kit + 선호하는 AI 도구

🏢 기존 Cursor 사용자, 규칙 체계화 필요하다면
   → Cursor Rules (.cursor/rules)

🚀 대규모 기능 개발, 여러 작업 병렬 실행하고 싶다면
   → Google Antigravity (Manager View)

💻 VS Code 유지하면서 AI 에이전트 원한다면
   → Kilo Code

🔄 여러 AI 도구를 자유롭게 전환하고 싶다면
   → GitHub Spec Kit (도구 중립적)
```

---

## 22.9 실전 워크플로우

### 22.9.1 GitHub Spec Kit + Claude Code

```bash
# 1. 프로젝트 초기화
spec-kit init --agent claude
# → .spec/ 디렉토리 생성

# 2. 명세 작성
spec-kit specify "결제 시스템 with Stripe 연동"
# → .spec/spec.md 생성
# → 직접 열어서 요구사항 보완

# 3. 기술 계획
spec-kit plan
# → .spec/plan.md 생성
# → 아키텍처 다이어그램, 파일 구조 포함

# 4. Claude Code에서 태스크 구현
claude
> 이 프로젝트의 .spec/plan.md를 읽고
> 첫 번째 태스크부터 순서대로 구현해줘.
> 각 태스크 완료 후 테스트 실행해줘.

# 5. 검증
spec-kit verify all
```

### 22.9.2 Cursor Rules + Agent Mode

```markdown
<!-- .cursor/rules/payment.md -->
---
globs: ["**/payment/**", "**/stripe/**"]
---

## 결제 모듈 규칙

### 보안
- 카드 정보는 절대 로깅하지 않음
- Stripe 웹훅은 서명 검증 필수
- 금액은 항상 정수(cents)로 처리

### 에러 처리
- Stripe 에러는 사용자 친화적 메시지로 변환
- 실패한 결제는 재시도 로직 포함
- 모든 결제 시도는 DB에 기록
```

```
Cursor 사용 흐름
───────────────
1. Ask Mode: "Stripe 결제 통합 계획 세워줘"
   → 구현 계획 검토

2. Agent Mode: "Payment 모듈 구현해줘"
   → Rules에 따라 구현

3. 결과 리뷰
   → Diff 확인, 승인/수정
```

### 22.9.3 Antigravity 멀티 에이전트

```
시나리오: 대시보드 리팩토링
────────────────────────────

1. Manager View에서 3개 작업 생성:
   - Agent A: 컴포넌트 분리
   - Agent B: API 레이어 정리
   - Agent C: 테스트 커버리지 향상

2. 각 에이전트에게 명세 전달:
   Agent A: "Dashboard.tsx를 5개 서브 컴포넌트로 분리해.
            각 컴포넌트는 단일 책임 원칙을 따라야 해."

3. 비동기 실행:
   - 개발자는 다른 미팅 참석
   - 에이전트들이 병렬로 작업

4. 결과 리뷰 (1시간 후):
   - 각 에이전트의 Artifacts 확인
   - 스크린샷으로 UI 변화 확인
   - 변경사항 승인 또는 수정 요청
```

---

## 22.10 Best Practices

### 22.10.1 SDD Best Practices

```
Spec-Driven Development Best Practices
───────────────────────────────────────

1. 명세에 시간 투자하기
   - 명세가 상세할수록 결과물 품질 향상
   - "나중에 고치면 되지"는 비용 증가의 원인

2. 점진적 구체화
   - 처음부터 완벽한 명세 불필요
   - 대략적 명세 → AI 피드백 → 구체화 반복

3. 검증 기준 포함
   - 각 요구사항에 테스트 케이스 명시
   - "어떻게 확인할 것인가"까지 정의

4. 기술 제약 명시
   - 사용할 라이브러리, 금지된 접근법
   - AI가 추측하지 않도록

5. 예시 데이터 제공
   - 입력/출력 예시
   - 엣지 케이스 시나리오
```

### 22.10.2 Agent-First Best Practices

```
Agent-First Development Best Practices
───────────────────────────────────────

1. 작은 것부터 위임
   - 첫 주: 테스트 작성만 위임
   - 신뢰 구축 후 범위 확대

2. 항상 버전 관리
   - 에이전트 실행 전 커밋
   - 실패해도 롤백 가능하게

3. 리뷰 시간 확보
   - 생성 속도 빨라도 리뷰는 필수
   - "빠르게 만들고 천천히 검토"

4. 규칙/가이드라인 문서화
   - .cursor/rules, AGENTS.md 활용
   - 에이전트도 규칙을 따르도록

5. 실패 사례 분석
   - 에이전트가 실수한 패턴 기록
   - 규칙에 반영하여 재발 방지
```

---

## 요약

```
Spec-Driven & Agent-First 개발 정리
───────────────────────────────────────────────

[ Vibe Coding ]
      │
      │ 문제: 모호함 → 추측 → 오류
      ▼
[ Spec-Driven Development ]
      │
      │ 해결: 명세 → 계획 → 태스크 → 검증
      ▼
[ Agent-First Development ]
      │
      │ 진화: 개발자=아키텍트, AI=구현자
      ▼
┌─────────────────────────────────────────────┐
│              도구 생태계                     │
├──────────────┬──────────────────────────────┤
│ 프로세스     │ GitHub Spec Kit             │
├──────────────┼──────────────────────────────┤
│ IDE 규칙    │ Cursor Rules, Kilo Config   │
├──────────────┼──────────────────────────────┤
│ Agent IDE   │ Antigravity, Cursor, Kilo   │
└──────────────┴──────────────────────────────┘
```

**핵심 메시지**: AI가 코드를 잘 작성하려면, 우리가 **요구사항을 잘 정의**해야 합니다. Vibe Coding의 시대는 끝나가고, 명세 중심의 체계적인 개발 방법론이 필요합니다.

---

## 참고 자료

- [GitHub Spec Kit 공식 저장소](https://github.com/github/spec-kit)
- [GitHub Blog: Spec-driven development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Cursor Documentation: Modes](https://cursor.com/docs/agent/modes)
- [Google Developers Blog: Build with Antigravity](https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/)

---

*다음 장에서는 실습을 통해 직접 Spec-Driven Development를 체험해봅니다.*
