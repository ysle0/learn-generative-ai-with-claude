# Chapter 12: MCP (Model Context Protocol)

> **이 장의 대상 독자:** 매일 AI 도구를 사용하지만, 그 내부가 어떻게 동작하는지 모르는 개발자
> 특히 Sequential Thinking MCP 서버를 이미 사용하고 있지만, MCP가 정확히 무엇인지 궁금했던 분들을 위해.

---

## 핵심 키워드 요약

| 키워드 | 정의 |
|--------|------|
| **MCP (Model Context Protocol)** | AI 모델을 외부 세계와 연결하는 표준 프로토콜 |
| **호스트 (Host)** | MCP 연결을 시작하는 AI 애플리케이션 (Claude Code, Cursor 등) |
| **클라이언트 (MCP Client)** | 호스트 내부에서 MCP 서버와의 연결을 관리하는 구성 요소 |
| **서버 (MCP Server)** | 도구, 리소스, 프롬프트 등의 기능을 제공하는 프로그램 |
| **도구 (Tool)** | 모델이 호출할 수 있는 함수 (SQL 쿼리 실행, 파일 생성 등) |
| **리소스 (Resource)** | 모델이 읽을 수 있는 데이터 (파일 내용, DB 스키마 등) |
| **프롬프트 템플릿 (Prompt Template)** | 재사용 가능한 프롬프트 구조 |
| **stdio** | 로컬 서버에서 가장 흔히 쓰이는 전송 프로토콜 |
| **SSE (Server-Sent Events)** | 원격 서버를 위한 HTTP 기반 전송 프로토콜 |
| **Streamable HTTP** | SSE를 대체하는 최신 전송 프로토콜 |
| **Sequential Thinking** | 구조화된 사고 과정을 지원하는 MCP 서버 |
| **전송 프로토콜 (Transport Protocol)** | 호스트와 서버 간 메시지를 주고받는 통신 방식 |

---

## 1. MCP란 무엇인가

### AI를 위한 USB 포트

USB가 없던 시절에는 프린터마다 다른 케이블, 다른 드라이버가 필요했습니다.
USB가 등장하면서 "하나의 표준 커넥터"로 모든 장치를 연결할 수 있게 되었습니다.
**MCP(Model Context Protocol)** 는 AI 모델을 위한 USB입니다.

```
MCP 이전:                          MCP 이후:

파일시스템 --[커스텀API]--> AI      파일시스템 --[MCP]-->|
DB       --[플러그인]---> AI       DB       --[MCP]-->| AI 모델
웹검색    --[자체구현]---> AI       웹검색    --[MCP]-->|
Git      --[직접코딩]---> AI       Git      --[MCP]-->|
```

MCP는 Anthropic이 2024년에 만들어 공개한 **개방형 표준(Open Standard)** 입니다.
특정 회사의 AI에만 국한되지 않으며, 어떤 AI 애플리케이션이든 MCP를 통해
외부 도구와 데이터에 접근할 수 있습니다.

### 왜 MCP가 필요한가

```
M개의 AI 앱 x N개의 도구 = M * N 개의 커스텀 통합 코드
MCP를 사용하면:    M + N 개의 MCP 구현으로 충분

예: AI 앱 3개 x 도구 5개 = 15개 --> MCP 사용 시 8개로 감소
```

---

## 2. 아키텍처: 호스트, 클라이언트, 서버

MCP의 아키텍처는 세 가지 핵심 구성 요소로 이루어져 있습니다.

```
+------------------------------------------------------------------+
|  호스트 (Host) - AI 애플리케이션                                    |
|  예: Claude Code, Cursor, Windsurf                                |
|                                                                    |
|  +------------------------+    +------------------------+          |
|  | MCP 클라이언트 A       |    | MCP 클라이언트 B       |          |
|  | (1:1 연결)             |    | (1:1 연결)             |          |
|  +----------+-------------+    +----------+-------------+          |
+-------------|-----------------------------|-----------------------+
              |  (전송 프로토콜)              |  (전송 프로토콜)
     +--------v----------+        +---------v---------+
     | MCP 서버 A        |        | MCP 서버 B        |
     | (파일시스템 접근)  |        | (데이터베이스)     |
     | - 도구 (Tools)     |        | - 도구 (Tools)     |
     | - 리소스(Resources)|        | - 리소스(Resources)|
     | - 프롬프트(Prompts)|        | - 프롬프트(Prompts)|
     +--------------------+        +--------------------+
```

### 호스트 (Host)

호스트는 사용자가 직접 상호작용하는 AI 애플리케이션입니다. MCP 연결의
시작점이며, 보안 정책을 관리합니다. Claude Code, Cursor, Claude Desktop 등이
대표적인 호스트입니다. 개발자 비유로 말하면, 호스트는 **웹 브라우저**와 같습니다.
브라우저가 여러 웹사이트에 연결하듯, 호스트는 여러 MCP 서버에 연결합니다.

### 클라이언트 (MCP Client)

클라이언트는 호스트 내부에서 동작하며, 각 MCP 서버와 **1:1 연결**을
유지합니다. 한 호스트 안에 여러 클라이언트가 존재할 수 있지만, 각
클라이언트는 정확히 하나의 서버만 담당합니다.

### 서버 (MCP Server)

서버는 실제 기능을 제공하는 프로그램입니다. 도구(Tools), 리소스(Resources),
프롬프트(Prompts)의 세 가지 프리미티브를 노출할 수 있습니다.

---

## 3. 핵심 프리미티브 (Core Primitives)

```
+------------------------------------------------------------------+
|  +------------------+  +------------------+  +------------------+  |
|  |   도구 (Tools)   |  | 리소스(Resources)|  |프롬프트(Prompts) |  |
|  |  "실행하는 것"    |  |  "읽는 것"       |  |  "템플릿"        |  |
|  |                  |  |                  |  |                  |  |
|  | - SQL 쿼리 실행  |  | - 파일 내용 읽기 |  | - 코드 리뷰      |  |
|  | - 파일 생성      |  | - DB 스키마 조회 |  |   요청 템플릿    |  |
|  | - API 호출       |  | - 설정 값 확인   |  | - 버그 리포트    |  |
|  +------------------+  +------------------+  +------------------+  |
|  제어: 모델이 호출     제어: 앱/모델이 선택  제어: 사용자가 선택   |
+------------------------------------------------------------------+
```

### 도구 (Tools)

도구는 AI 모델이 **호출할 수 있는 함수**입니다. 모델이 사용자의 요청을
분석한 뒤, 필요하다고 판단하면 도구를 호출합니다. REST API의 엔드포인트와
비슷합니다. 이름, 설명, 입력 스키마(JSON Schema)가 있고, 모델은 이 정보를
보고 언제 어떻게 호출할지 결정합니다.

```json
{
    "name": "run_sql_query",
    "description": "PostgreSQL 데이터베이스에서 SQL 쿼리를 실행합니다",
    "inputSchema": {
        "type": "object",
        "properties": {
            "query": { "type": "string", "description": "실행할 SQL 쿼리" }
        },
        "required": ["query"]
    }
}
```

### 리소스 (Resources)

리소스는 모델이 **읽을 수 있는 데이터**입니다. 도구와 달리 부작용(Side Effect)이
없는 읽기 전용 데이터입니다. REST API 비유로, 도구가 POST/PUT/DELETE라면
리소스는 GET에 해당합니다.

```
리소스 URI 예시:
  file:///home/user/project/src/main.ts    (파일 내용)
  db://production/users/schema              (DB 스키마)
  config://app/settings                     (앱 설정)
```

### 프롬프트 템플릿 (Prompt Templates)

프롬프트 템플릿은 재사용 가능한 **미리 정의된 프롬프트**입니다.
사용자가 선택하여 사용하며, 일관된 작업 품질을 보장합니다.
예를 들어 "코드 리뷰 요청" 템플릿에는 언어와 코드를 인자로 받아
일정한 형식의 리뷰 요청 프롬프트를 생성합니다.

---

## 4. 전송 프로토콜 (Transport Protocols)

호스트와 서버가 메시지를 주고받는 방법에는 세 가지가 있습니다.

### stdio (Standard I/O)

가장 흔하게 사용되는 방식입니다. 호스트가 서버 프로세스를 직접 실행하고,
표준 입출력(stdin/stdout)으로 JSON-RPC 메시지를 교환합니다.

```
+----------+  stdin (JSON-RPC)   +----------+
|   호스트  | -----------------> | MCP 서버 |  (로컬 프로세스)
|          | <----------------- |          |
+----------+  stdout (JSON-RPC)  +----------+
```

- **장점**: 설정이 간단하고, 네트워크 설정 불필요
- **용도**: 파일시스템, Git, Sequential Thinking 등 로컬 서버

### HTTP + SSE (Server-Sent Events)

원격 서버와 통신할 때 사용합니다. 클라이언트에서 서버로는 HTTP POST를,
서버에서 클라이언트로는 SSE 스트림을 사용합니다.

```
+----------+  HTTP POST          +----------+
|   호스트  | -----------------> | MCP 서버 |  (원격 서버)
|          | <----------------- |          |
+----------+  SSE 스트림          +----------+
```

### Streamable HTTP

SSE 방식을 개선한 최신 전송 프로토콜입니다. 단일 HTTP 엔드포인트(`/mcp`)로
양방향 통신을 처리하며, 상태 비저장(Stateless) 서버도 지원할 수 있어
인프라 호환성이 우수합니다. 차세대 원격 MCP 서버의 표준으로 자리잡고 있습니다.

---

## 5. Sequential Thinking MCP -- 매일 쓰는 그것

여러분이 매일 사용하는 Sequential Thinking MCP 서버가 내부적으로
어떻게 동작하는지 알아봅시다.

### 무엇을 하는 서버인가

Sequential Thinking 서버는 AI 모델에게 **구조화된 사고 과정**을 제공합니다.
복잡한 문제를 단계별로 나누어 생각하고, 필요하면 이전 단계로 돌아가
수정할 수 있게 합니다.

```
일반적인 AI 응답:
  질문 --> [한 번에 최종 답변]

Sequential Thinking 적용:
  질문 --> [사고 1] --> [사고 2] --> [사고 3] --> ... --> [최종 답변]
                  \                     |
                   \--- [사고 2-b] ---/    (분기 및 수정 가능)
```

### 내부 동작 흐름

서버가 제공하는 도구는 `sequentialthinking` 하나입니다. 모델이 이 도구를
반복 호출하며 사고를 쌓아갑니다.

```
1단계: 모델이 첫 번째 사고를 도구에 전달
  { thought: "문제를 분해해보자...",
    thoughtNumber: 1, totalThoughts: 5, nextThoughtNeeded: true }

2단계: 서버가 기록하고, 모델이 다음 사고를 생성
  { thought: "첫 접근의 문제점은...",
    thoughtNumber: 2, totalThoughts: 5,
    isRevision: true, revisesThought: 1,    <-- 수정 가능
    nextThoughtNeeded: true }

3단계: 필요한 만큼 반복 (totalThoughts도 동적 조정 가능)

종료: nextThoughtNeeded: false 로 사고 체인 종료
```

### 세 가지 핵심 기능

**사고 체인 (Thought Chains)**: 복잡한 추론을 순서대로 기록합니다.
각 사고는 번호가 매겨지고, 이전 사고의 맥락 위에 다음 사고를 쌓습니다.

**분기 (Branching)**: `branchFromThought` 필드로 특정 사고 지점에서
다른 접근 방식을 시도할 수 있습니다.

**수정 (Revision)**: `isRevision`과 `revisesThought`로 이전 단계의
잘못된 사고를 되돌아가 수정합니다.

개발자 비유로 말하면, 이것은 **Git의 브랜치와 리베이스**와 같습니다.
사고의 main 브랜치를 따라가다가, feature 브랜치를 만들고, 잘못된 커밋은 수정합니다.

---

## 6. 나만의 MCP 서버 만들기

### 사용 가능한 SDK

| SDK | 언어 | 패키지 |
|-----|------|--------|
| TypeScript SDK | TypeScript/JavaScript | `@modelcontextprotocol/sdk` |
| Python SDK | Python | `mcp` |
| Kotlin SDK | Kotlin/JVM | `io.modelcontextprotocol:kotlin-sdk` |
| C# SDK | C#/.NET | `ModelContextProtocol` |

### TypeScript 예제: 간단한 MCP 서버

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

// 1. 서버 인스턴스 생성
const server = new McpServer({
  name: "my-first-mcp-server",
  version: "1.0.0",
});

// 2. 도구 등록 -- 모델이 호출할 수 있는 함수
server.tool(
  "get_current_time",
  "현재 시스템 시간을 반환합니다",
  { timezone: { type: "string" as const, description: "타임존 (예: Asia/Seoul)" } },
  async ({ timezone }) => {
    const now = new Date().toLocaleString("ko-KR", {
      timeZone: timezone ?? "Asia/Seoul",
    });
    return { content: [{ type: "text", text: `현재 시간: ${now}` }] };
  }
);

// 3. 리소스 등록 -- 모델이 읽을 수 있는 데이터
server.resource(
  "server-info",
  "info://server/status",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      mimeType: "application/json",
      text: JSON.stringify({
        name: "my-first-mcp-server",
        uptime: process.uptime(),
      }),
    }],
  })
);

// 4. stdio 전송으로 서버 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

Claude Code에서 이 서버를 사용하려면 설정 파일에 다음을 추가합니다.

```json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["tsx", "/path/to/my-mcp-server/src/index.ts"]
    }
  }
}
```

### Python 예제: FastMCP 패턴

Python SDK의 `FastMCP`는 Flask/FastAPI처럼 데코레이터로 서버를 구축합니다.

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-python-server")

@mcp.tool()
def calculate_bmi(weight_kg: float, height_cm: float) -> str:
    """체질량지수(BMI)를 계산합니다."""
    height_m = height_cm / 100
    bmi = weight_kg / (height_m ** 2)
    return f"BMI: {bmi:.1f}"

@mcp.resource("config://app/version")
def get_version() -> str:
    return "1.0.0"

@mcp.prompt()
def debug_error(error_message: str) -> str:
    return f"다음 에러를 분석해 주세요:\n에러: {error_message}\n"

if __name__ == "__main__":
    mcp.run()
```

---

## 7. MCP 생태계 (Ecosystem)

### 주요 서버

```
+------------------------------------------------------------------+
|                     MCP 서버 생태계                                |
|                                                                    |
|  [파일시스템]           로컬 파일 읽기/쓰기/검색                    |
|  [Git]                 저장소 관리, 커밋 이력, 브랜치 조작          |
|  [PostgreSQL]          데이터베이스 쿼리 실행, 스키마 조회          |
|  [Brave Search]        웹 검색, 지역 검색                         |
|  [Puppeteer]           브라우저 자동화, 스크린샷                   |
|  [Slack]               메시지 전송, 채널 관리                      |
|  [GitHub]              이슈, PR 관리, 코드 검색                    |
|  [Sequential Thinking] 구조화된 추론 지원                          |
|                                                                    |
|  그 외 수백 개의 커뮤니티 서버들...                                 |
+------------------------------------------------------------------+
```

### 서버 검색과 설치

- **공식 저장소**: github.com/modelcontextprotocol/servers
- **패키지 레지스트리**: npmjs.com, PyPI에서 MCP 서버 패키지 검색
- **커뮤니티 목록**: 서드파티 레지스트리와 큐레이션 목록

```bash
# npm을 통한 MCP 서버 실행 예시
npx -y @modelcontextprotocol/server-filesystem /path/to/allowed/dir

# Python MCP 서버 실행 예시
uvx mcp-server-git --repository /path/to/repo
```

---

## 8. MCP vs 다른 접근 방식

```
+------------------------------------------------------------------+
|             AI 도구 연동 방식 비교                                  |
|                                                                    |
|  방식              | 표준화 | 전송 계층 | 상태 관리 | 생태계        |
|  ------------------|--------|----------|-----------|--------------|
|  MCP               | O (개방)| 다양    | 세션 유지  | 개방형       |
|  OpenAI Func. Call | X (독점)| HTTP    | 무상태    | OpenAI 중심  |
|  LangChain Tools   | X (독점)| 다양    | 프레임워크 | Python 중심  |
+------------------------------------------------------------------+
```

**OpenAI Function Calling과의 차이**: Function Calling은 모델이 함수를
호출하는 형식(JSON 스키마)만 정의합니다. 실제 함수 실행, 연결 관리,
전송 프로토콜은 개발자가 직접 구현해야 합니다. MCP는 연결부터 실행까지
전체 파이프라인을 표준화합니다.

```
OpenAI Function Calling:
  모델 --> "이 함수를 호출해줘" (JSON) --> [개발자가 직접 구현]

MCP:
  모델 --> MCP 클라이언트 --> 전송 프로토콜 --> MCP 서버 --> 도구 실행
  (전체 파이프라인이 표준화됨)
```

**LangChain Tools와의 차이**: LangChain은 Python 프레임워크 안에서
도구를 정의하고 실행합니다. LangChain 앱 내부에서만 사용 가능하며,
프레임워크에 종속됩니다. MCP는 프레임워크에 독립적인 프로토콜이므로,
어떤 언어, 어떤 AI 앱에서든 사용할 수 있습니다.

---

## 9. 보안 고려사항 (Security Considerations)

MCP는 AI 모델에게 외부 시스템 접근 권한을 부여하므로, 보안이 핵심입니다.

```
+------------------------------------------------------------------+
|                    MCP 보안 모델                                   |
|                                                                    |
|  1. 최소 권한 원칙 (Principle of Least Privilege)                  |
|     - 서버는 필요한 최소한의 기능만 노출                             |
|     - 파일시스템 서버는 지정된 디렉토리만 접근 가능                   |
|                                                                    |
|  2. 사용자 동의 (User Consent)                                     |
|     - 도구 호출 전 사용자에게 확인을 요청                            |
|     - "SQL 쿼리를 실행하겠습니다. 허용하시겠습니까?"                  |
|                                                                    |
|  3. 능력 기반 접근 (Capability-Based Access)                       |
|     - 서버가 자신의 기능을 명시적으로 선언                           |
|     - 호스트가 어떤 기능을 허용할지 결정                             |
|                                                                    |
|  4. 전송 계층 보안                                                  |
|     - 원격 서버는 HTTPS/TLS 필수                                   |
|     - 인증 토큰을 통한 서버 접근 제어                                |
+------------------------------------------------------------------+
```

### 실무에서 주의할 점

```
위험한 패턴:
  MCP 서버가 임의의 시스템 명령을 실행할 수 있게 구성
  --> 모델이 rm -rf / 같은 명령을 실행할 수 있음

안전한 패턴:
  MCP 서버가 허용 목록(allowlist)에 있는 명령만 실행
  --> 사전 정의된 안전한 작업만 가능
```

- **입력 검증**: 모델이 보내는 모든 입력을 서버 측에서 검증
- **출력 제한**: 민감한 정보(비밀번호, API 키)가 모델에 노출되지 않도록 필터링
- **접근 범위 제한**: 파일시스템은 특정 디렉토리로, DB는 읽기 전용 등
- **로깅**: 모든 도구 호출을 기록하여 감사(Audit) 추적 가능하게 유지

---

## 10. 정리: 이 장에서 기억할 것

1. **MCP는 AI를 위한 USB**입니다. AI 모델이 외부 도구와 데이터에
   접근하는 방법을 표준화한 개방형 프로토콜입니다.

2. **아키텍처는 호스트-클라이언트-서버** 3계층입니다.
   호스트가 AI 앱, 클라이언트가 연결 관리, 서버가 기능을 제공합니다.

3. **세 가지 프리미티브**: 도구(실행), 리소스(읽기), 프롬프트(템플릿)가
   MCP 서버가 제공하는 기능의 전부입니다.

4. **전송 프로토콜**: 로컬은 stdio, 원격은 HTTP+SSE 또는
   Streamable HTTP를 사용합니다.

5. **Sequential Thinking**은 사고 체인, 분기, 수정을 통해
   AI의 구조화된 추론을 지원하는 MCP 서버입니다.

6. **보안은 최소 권한, 사용자 동의, 능력 기반 접근**이 핵심입니다.
   MCP 서버에 과도한 권한을 부여하지 마세요.

> **다음 장 예고:** Chapter 13에서는 MCP 위에서 동작하는 **AI 에이전트
> (AI Agent)** 의 개념을 다룹니다. 단순히 도구를 한 번 호출하는 것을 넘어,
> 여러 도구를 조합하여 복잡한 작업을 자율적으로 수행하는 에이전트가
> 어떻게 설계되는지 알아보겠습니다.
