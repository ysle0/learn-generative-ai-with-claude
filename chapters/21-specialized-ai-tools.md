# Chapter 21: 특화된 AI 도구 — Kilo Code, Bezi, Unity AI

> **"범용 도구가 좋지만, 특화된 도구가 빛나는 순간이 있다."**
>
> Claude Code나 Cursor 같은 범용 AI 코딩 도구는 강력하지만,
> 특정 도메인(게임 개발, 3D 디자인, VS Code 통합)에 특화된 도구들은
> 해당 영역에서 더 깊은 통합과 최적화된 경험을 제공합니다.

---

## 핵심 키워드 요약

| 키워드 | 설명 |
|--------|------|
| **Kilo Code** | VS Code 확장 기반 AI 코딩 어시스턴트 (다중 모델 지원) |
| **Bezi** | AI 기반 3D 디자인 및 프로토타이핑 도구 |
| **Unity Muse** | Unity의 AI 기반 코드/에셋 생성 도구 |
| **Unity Sentis** | Unity 런타임에서 AI 모델 실행을 위한 추론 엔진 |
| **ML-Agents** | Unity 환경에서 AI 에이전트를 훈련시키는 프레임워크 |

---

## 21.1 Kilo Code — VS Code 네이티브 AI 코딩

### 21.1.1 Kilo Code란?

**Kilo Code**는 VS Code에서 직접 실행되는 AI 코딩 어시스턴트입니다. Cursor의 에이전트 모드와 유사한 기능을 제공하면서도, 기존 VS Code 환경을 그대로 활용할 수 있다는 것이 강점입니다.

```
Kilo Code vs 다른 AI 코딩 도구
─────────────────────────────────
┌────────────┬──────────────┬─────────────┬───────────────┐
│            │ Kilo Code    │ Cursor      │ Claude Code   │
├────────────┼──────────────┼─────────────┼───────────────┤
│ 환경       │ VS Code 확장 │ 독립 IDE    │ CLI           │
│ 모델       │ 다중 선택    │ 다중 선택   │ Claude만      │
│ 가격       │ 무료+유료    │ 구독제      │ API 종량제    │
│ 에이전트   │ ✅           │ ✅          │ ✅            │
│ 기존환경   │ 그대로 유지  │ 새 IDE 학습 │ 터미널 기반   │
└────────────┴──────────────┴─────────────┴───────────────┘
```

### 21.1.2 핵심 기능

```
Kilo Code 기능 맵
├── 🤖 AI Chat
│   ├── 코드베이스 컨텍스트 인식
│   ├── 파일 참조 (@file)
│   ├── 선택 영역 참조
│   └── 멀티턴 대화
│
├── 🔧 AI Edit
│   ├── 인라인 코드 수정
│   ├── Diff 미리보기
│   ├── Accept/Reject 선택
│   └── 다중 파일 수정
│
├── 🚀 Agent Mode
│   ├── 자율적 작업 수행
│   ├── 파일 생성/수정/삭제
│   ├── 터미널 명령 실행
│   └── 에러 자동 수정
│
├── 📝 Code Completion
│   ├── 실시간 자동완성
│   ├── 전체 함수 완성
│   └── 주석 기반 생성
│
└── 🔍 Code Explanation
    ├── 선택 코드 설명
    ├── 복잡한 로직 분석
    └── 문서 생성
```

### 21.1.3 설치 및 설정

```bash
# VS Code에서 설치
1. Extensions (Ctrl+Shift+X) 열기
2. "Kilo Code" 검색
3. Install 클릭

# 또는 CLI로 설치
code --install-extension kilocode.kilo-code
```

**설정 (settings.json)**:

```json
{
  "kilocode.defaultModel": "claude-3-5-sonnet",
  "kilocode.apiKeys": {
    "anthropic": "${env:ANTHROPIC_API_KEY}",
    "openai": "${env:OPENAI_API_KEY}"
  },
  "kilocode.agent.autoApprove": false,
  "kilocode.chat.contextFiles": 10,
  "kilocode.completion.enabled": true
}
```

### 21.1.4 사용법

#### AI Chat 사용하기

```
1. Cmd+Shift+P (또는 Ctrl+Shift+P)
2. "Kilo: Open Chat" 선택
3. 질문 입력

예시 프롬프트:
────────────
@src/api/users.ts 이 파일에서 에러 핸들링을 개선해줘

@selection 이 코드가 뭘 하는지 설명해줘

이 프로젝트의 아키텍처를 분석해줘
```

#### Agent Mode 사용하기

```
1. Chat 패널에서 "Agent Mode" 토글 활성화
2. 작업 요청

예시:
────
"로그인 API에 JWT 토큰 갱신 기능을 추가해줘.
필요한 파일 생성하고 테스트도 작성해줘."

Agent가 수행하는 작업:
├── 1. 기존 코드 분석
├── 2. 수정 계획 수립
├── 3. src/api/auth.ts 수정
├── 4. src/utils/jwt.ts 생성
├── 5. tests/auth.test.ts 작성
└── 6. 변경사항 요약 제공

각 단계에서 사용자 승인 요청 (autoApprove: false인 경우)
```

### 21.1.5 Kilo Code Use Cases

```
Use Case 1: 레거시 코드 현대화
───────────────────────────────
입력: "이 jQuery 코드를 React 훅으로 변환해줘"
결과:
- 클래스 컴포넌트 → 함수형 컴포넌트
- jQuery DOM 조작 → React state
- 콜백 → async/await

Use Case 2: 테스트 코드 생성
───────────────────────────────
입력: "@src/utils/validation.ts 이 파일의 모든 함수에 대한
      Jest 테스트를 작성해줘. 엣지 케이스 포함해서."
결과:
- 각 함수별 테스트 파일 생성
- 정상 케이스 + 엣지 케이스
- 목(mock) 설정 포함

Use Case 3: API 문서 생성
───────────────────────────────
입력: "@src/api/ 폴더의 모든 엔드포인트에 대한
      OpenAPI 스펙을 생성해줘"
결과:
- openapi.yaml 파일 생성
- 각 엔드포인트의 요청/응답 스키마
- 예시 값 포함

Use Case 4: 버그 디버깅
───────────────────────────────
입력: "이 에러 로그를 분석하고 원인을 찾아줘:
      [에러 스택트레이스 붙여넣기]"
결과:
- 에러 원인 분석
- 관련 코드 위치 식별
- 수정 방안 제안 + 코드 패치
```

### 21.1.6 Best Practices

```
Kilo Code Best Practices
─────────────────────────

1. 컨텍스트를 명확히
   ❌ "이거 고쳐줘"
   ✅ "@src/api/users.ts:45-60 이 부분에서 null 체크 추가해줘"

2. Agent Mode는 신중하게
   - 큰 변경은 먼저 계획을 물어보기
   - autoApprove: false 권장
   - 변경 전 Git 상태 확인

3. 모델 선택 전략
   - 간단한 완성: GPT-4o-mini (빠름, 저렴)
   - 복잡한 리팩토링: Claude 3.5 Sonnet
   - 코드 분석: GPT-4 또는 Claude

4. @mention 적극 활용
   - @file: 특정 파일 참조
   - @folder: 폴더 전체 참조
   - @web: 웹 검색 결과 포함

5. 프로젝트 설정 파일 활용
   - .kilocode 파일로 프로젝트별 설정
   - 팀원과 설정 공유
```

---

## 21.2 Bezi — AI 기반 3D 디자인

### 21.2.1 Bezi란?

**Bezi**는 3D 인터페이스와 공간 컴퓨팅 디자인을 위한 AI 도구입니다. Figma가 2D UI 디자인의 표준이라면, Bezi는 3D/XR 인터페이스 디자인을 위한 도구를 목표로 합니다.

```
Bezi의 타겟 영역
────────────────────────────────────────────────
┌─────────────────────────────────────────────┐
│            공간 컴퓨팅 (Spatial Computing)    │
├──────────────┬──────────────┬───────────────┤
│     VR       │     AR       │    MR         │
│   (가상현실)  │  (증강현실)   │  (혼합현실)    │
├──────────────┴──────────────┴───────────────┤
│  • Apple Vision Pro 앱                       │
│  • Meta Quest 앱                             │
│  • 3D 웹 인터페이스                           │
│  • 게임 UI/UX                                │
└─────────────────────────────────────────────┘
```

### 21.2.2 핵심 기능

```
Bezi 기능 맵
├── 🎨 3D 디자인
│   ├── 직관적인 3D 오브젝트 조작
│   ├── 재질(Material) 편집
│   ├── 조명(Lighting) 설정
│   └── 카메라 앵글 관리
│
├── 🤖 AI 기능
│   ├── 텍스트 → 3D 모델 생성
│   ├── 이미지 → 3D 변환
│   ├── 레이아웃 자동 제안
│   └── 스타일 트랜스퍼
│
├── 🔄 인터랙션 디자인
│   ├── 상태(State) 기반 전환
│   ├── 애니메이션 타임라인
│   ├── 제스처 인터랙션
│   └── 음성 명령 프로토타이핑
│
├── 👥 협업
│   ├── 실시간 멀티플레이어 편집
│   ├── 버전 히스토리
│   ├── 코멘트 & 피드백
│   └── 디자인 시스템 공유
│
└── 🚀 내보내기
    ├── Unity 패키지
    ├── Unreal 프로젝트
    ├── glTF / USDZ
    └── React Three Fiber 코드
```

### 21.2.3 설치 및 시작

```bash
# Bezi는 웹 기반 + 데스크톱 앱
# 1. https://bezi.com 가입
# 2. 데스크톱 앱 다운로드 (macOS/Windows)

# 또는 웹에서 바로 시작
# https://app.bezi.com
```

### 21.2.4 기본 워크플로우

```
Bezi 작업 흐름
─────────────

1. 프로젝트 생성
   ├── 타겟 플랫폼 선택 (Vision Pro, Quest, Web 등)
   ├── 기본 씬 템플릿 선택
   └── 디자인 시스템 연결 (선택)

2. 3D 씬 구성
   ├── 기본 오브젝트 배치 (패널, 버튼, 텍스트)
   ├── 3D 모델 임포트
   ├── 재질 및 조명 설정
   └── 공간 배치 조정

3. AI 활용
   ├── "버튼 그룹을 더 현대적으로 바꿔줘"
   ├── "이 레이아웃을 Vision Pro 스타일로"
   ├── "아이콘을 3D로 변환해줘"
   └── "접근성 개선 제안해줘"

4. 인터랙션 설계
   ├── 상태 정의 (기본, 호버, 활성)
   ├── 전환 애니메이션
   ├── 제스처 트리거 설정
   └── 프로토타입 테스트

5. 내보내기
   └── 개발 플랫폼에 맞게 에셋 추출
```

### 21.2.5 AI 기능 상세

#### 텍스트 → 3D 생성

```
입력: "미래적인 홀로그램 스타일의 대시보드 패널"

AI가 생성:
├── 반투명 유리 재질의 패널
├── 네온 글로우 테두리
├── 플로팅 데이터 시각화 요소
└── 적절한 배치와 간격
```

#### 스타일 트랜스퍼

```
입력: [기존 2D UI 이미지] + "Apple Vision Pro 스타일로 변환"

AI가 수행:
├── 2D 요소를 3D 공간에 배치
├── visionOS 디자인 가이드라인 적용
├── 적절한 깊이감 추가
├── 글래스모피즘 재질 적용
└── 공간 인터랙션 패턴 제안
```

### 21.2.6 Use Cases

```
Use Case 1: Vision Pro 앱 프로토타이핑
─────────────────────────────────────
목표: 새로운 명상 앱의 3D UI 디자인
과정:
1. Bezi에서 visionOS 템플릿 선택
2. AI로 "차분한 자연 환경의 명상 공간" 생성
3. 메뉴와 컨트롤 UI 배치
4. 호흡 가이드 애니메이션 추가
5. Unity로 내보내 실제 빌드

Use Case 2: 쇼핑 AR 경험 디자인
─────────────────────────────────────
목표: 가구 AR 배치 앱 UI 디자인
과정:
1. 가구 3D 모델 임포트
2. AR 컨트롤 UI (회전, 크기 조절) 배치
3. 제품 정보 패널 디자인
4. "구매" 플로우 프로토타이핑
5. 사용자 테스트용 프로토타입 공유

Use Case 3: 게임 HUD 디자인
─────────────────────────────────────
목표: VR 슈팅 게임의 HUD 디자인
과정:
1. 홀로그램 스타일의 체력/탄약 UI
2. 미니맵의 3D 표현
3. 인터랙션 피드백 애니메이션
4. 다양한 상태 (전투, 평화, 경고) 디자인
5. Unity 패키지로 내보내기
```

### 21.2.7 Best Practices

```
Bezi Best Practices
───────────────────

1. 플랫폼 가이드라인 숙지
   - visionOS Human Interface Guidelines
   - Meta Quest 디자인 원칙
   - 각 플랫폼의 컨벤션 따르기

2. 실제 디바이스에서 테스트
   - 시뮬레이터와 실제는 다름
   - 특히 크기감, 거리감 확인 필수

3. 성능 고려
   - 폴리곤 수 최적화
   - 텍스처 해상도 적정 수준 유지
   - 동시 렌더링 오브젝트 수 제한

4. 접근성
   - 색각 이상자를 위한 대비
   - 텍스트 크기와 가독성
   - 인터랙션 영역 크기 (특히 VR에서)

5. 협업
   - 디자인 시스템 일관성 유지
   - 개발자와 일찍 소통 (내보내기 포맷)
   - 버전 관리 철저히
```

---

## 21.3 Unity AI — 게임 개발자를 위한 AI 도구

### 21.3.1 Unity AI 생태계 개요

Unity는 게임 개발을 위한 종합 AI 도구 세트를 제공합니다:

```
Unity AI 도구 생태계
─────────────────────────────────────────────────
┌─────────────────────────────────────────────┐
│              Unity AI Platform               │
├──────────────┬──────────────┬───────────────┤
│  Unity Muse  │Unity Sentis  │  ML-Agents    │
│  (생성 AI)    │  (추론 엔진)  │  (학습 환경)   │
├──────────────┼──────────────┼───────────────┤
│• 코드 생성    │• 모델 실행    │• 강화학습     │
│• 텍스처 생성  │• ONNX 지원   │• 모방학습     │
│• 스프라이트   │• GPU 가속    │• 행동 복제    │
│• 애니메이션   │• 크로스플랫폼 │• 멀티에이전트 │
└──────────────┴──────────────┴───────────────┘
```

### 21.3.2 Unity Muse — 생성형 AI 도구

#### Unity Muse란?

Unity Muse는 Unity 에디터 내에서 AI를 활용해 코드, 텍스처, 스프라이트 등을 생성하는 도구입니다.

#### 주요 기능

```
Unity Muse 기능
├── 💬 Muse Chat
│   ├── Unity API 관련 질문 응답
│   ├── 코드 스니펫 생성
│   ├── 에러 해결 도움
│   └── 베스트 프랙티스 안내
│
├── 🎨 Muse Texture
│   ├── 텍스트 → 텍스처 생성
│   ├── 타일링 가능한 텍스처
│   ├── PBR 맵 자동 생성
│   └── 스타일 전이
│
├── 🖼️ Muse Sprite
│   ├── 2D 스프라이트 생성
│   ├── 애니메이션 시트
│   ├── UI 요소
│   └── 아이콘
│
└── ✏️ Muse Sketch (베타)
    ├── 스케치 → 완성 이미지
    └── 빠른 컨셉 아트
```

#### 설치 및 설정

```csharp
// 1. Unity 2022.3 LTS 이상 필요
// 2. Package Manager에서 설치:
//    Window > Package Manager > + > Add package by name
//    "com.unity.muse.chat"
//    "com.unity.muse.texture"
//    "com.unity.muse.sprite"

// 3. Unity 계정 로그인 및 Muse 구독 활성화
```

#### Muse Chat 사용 예시

```
예시 1: 코드 생성
───────────────
Q: "플레이어가 적에게 닿으면 데미지를 받는 스크립트 만들어줘"

A: (Muse가 생성)
using UnityEngine;

public class PlayerHealth : MonoBehaviour
{
    [SerializeField] private int maxHealth = 100;
    private int currentHealth;

    public event System.Action<int> OnHealthChanged;
    public event System.Action OnDeath;

    void Start()
    {
        currentHealth = maxHealth;
    }

    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Enemy"))
        {
            TakeDamage(10);
        }
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        OnHealthChanged?.Invoke(currentHealth);

        if (currentHealth <= 0)
        {
            Die();
        }
    }

    private void Die()
    {
        OnDeath?.Invoke();
        // 추가 사망 로직
    }
}

예시 2: 문제 해결
───────────────
Q: "NullReferenceException이 계속 발생해.
   GetComponent<Rigidbody>() 결과가 null이야"

A: 이 문제는 보통 다음 원인 중 하나입니다:

1. Rigidbody 컴포넌트가 오브젝트에 없음
   → Inspector에서 Add Component > Rigidbody 추가

2. Start() 전에 Awake()에서 참조해야 하는 경우
   → 초기화 시점 확인

권장 패턴:
private Rigidbody _rb;

void Awake()
{
    _rb = GetComponent<Rigidbody>();
    if (_rb == null)
    {
        Debug.LogError($"Rigidbody missing on {gameObject.name}");
    }
}
```

#### Muse Texture 사용

```
워크플로우:
1. Window > AI > Muse Texture 열기
2. 프롬프트 입력: "mossy stone wall, seamless, 4K"
3. 생성 클릭
4. 여러 변형 중 선택
5. PBR 맵 자동 생성 (Normal, Roughness, AO 등)
6. 프로젝트에 저장

프롬프트 팁:
─────────────
✅ "seamless brick texture, weathered, medieval style"
✅ "sci-fi metal panel, scratched, blue accent lights"
✅ "cartoon grass, stylized, bright colors, tileable"

❌ "texture" (너무 모호함)
❌ "cool looking thing" (구체적이지 않음)
```

### 21.3.3 Unity Sentis — 런타임 AI 추론

#### Unity Sentis란?

Unity Sentis는 게임 런타임에서 AI/ML 모델을 실행할 수 있게 해주는 추론 엔진입니다. 학습된 신경망 모델(ONNX 포맷)을 Unity 게임에 통합할 수 있습니다.

```
Sentis 활용 예시
────────────────────────────────────────────
┌─────────────────────────────────────────┐
│          게임에서 AI 모델 실행           │
├────────────────┬────────────────────────┤
│ 이미지 인식    │ • 플레이어 표정 인식    │
│                │ • 손 제스처 인식        │
│                │ • 물체 감지             │
├────────────────┼────────────────────────┤
│ 자연어 처리    │ • NPC 대화 이해         │
│                │ • 음성 명령 처리        │
├────────────────┼────────────────────────┤
│ 생성 AI       │ • 프로시저럴 콘텐츠     │
│                │ • 실시간 텍스처 변형    │
├────────────────┼────────────────────────┤
│ 게임플레이     │ • 적 AI 행동            │
│                │ • 난이도 동적 조절      │
│                │ • 플레이어 스타일 분석   │
└────────────────┴────────────────────────┘
```

#### 기본 사용법

```csharp
using Unity.Sentis;
using UnityEngine;

public class SentisExample : MonoBehaviour
{
    [SerializeField] private ModelAsset modelAsset;
    private Worker worker;

    void Start()
    {
        // 1. 모델 로드
        var model = ModelLoader.Load(modelAsset);

        // 2. 워커 생성 (GPU 가속)
        worker = new Worker(model, BackendType.GPUCompute);
    }

    public float[] RunInference(float[] inputData)
    {
        // 3. 입력 텐서 생성
        var inputTensor = new Tensor<float>(
            new TensorShape(1, inputData.Length),
            inputData
        );

        // 4. 추론 실행
        worker.Schedule(inputTensor);

        // 5. 결과 가져오기
        var outputTensor = worker.PeekOutput() as Tensor<float>;
        var result = outputTensor.DownloadToArray();

        inputTensor.Dispose();
        return result;
    }

    void OnDestroy()
    {
        worker?.Dispose();
    }
}
```

#### Use Case: 실시간 스타일 전이

```csharp
public class StyleTransfer : MonoBehaviour
{
    [SerializeField] private ModelAsset styleModel;
    [SerializeField] private RenderTexture sourceRT;
    [SerializeField] private RenderTexture outputRT;

    private Worker worker;

    void Start()
    {
        var model = ModelLoader.Load(styleModel);
        worker = new Worker(model, BackendType.GPUCompute);
    }

    void Update()
    {
        // 카메라 렌더 결과를 스타일 전이 모델에 입력
        var inputTensor = TextureConverter.ToTensor(sourceRT);

        worker.Schedule(inputTensor);

        var outputTensor = worker.PeekOutput();
        TextureConverter.ToRenderTexture(outputTensor, outputRT);

        inputTensor.Dispose();
    }
}
```

### 21.3.4 ML-Agents — AI 에이전트 훈련

#### ML-Agents란?

ML-Agents는 Unity 환경에서 강화학습(RL)을 통해 AI 에이전트를 훈련시키는 프레임워크입니다.

```
ML-Agents 학습 파이프라인
─────────────────────────────────────────

┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Unity     │    │  Python     │    │   학습된    │
│   환경      │◄──►│  학습기     │───►│   모델      │
│  (시뮬레이션)│    │ (PyTorch)   │    │  (ONNX)     │
└─────────────┘    └─────────────┘    └─────────────┘
      │                  │                   │
      ▼                  ▼                   ▼
 • 상태 관찰         • PPO/SAC          • Sentis로
 • 행동 수행         • 보상 최적화        게임에 통합
 • 보상 제공         • 정책 학습
```

#### 설치

```bash
# 1. Unity Package Manager에서 ML-Agents 설치
# com.unity.ml-agents

# 2. Python 환경 설정
pip install mlagents

# 3. 학습 시작
mlagents-learn config/trainer_config.yaml --run-id=MyTraining
```

#### 기본 에이전트 구현

```csharp
using Unity.MLAgents;
using Unity.MLAgents.Actuators;
using Unity.MLAgents.Sensors;
using UnityEngine;

public class SimpleAgent : Agent
{
    [SerializeField] private Transform target;
    private Rigidbody rb;

    public override void Initialize()
    {
        rb = GetComponent<Rigidbody>();
    }

    // 에피소드 시작 시 호출
    public override void OnEpisodeBegin()
    {
        // 위치 리셋
        transform.localPosition = new Vector3(0, 0.5f, 0);
        rb.velocity = Vector3.zero;

        // 타겟 랜덤 배치
        target.localPosition = new Vector3(
            Random.Range(-4f, 4f),
            0.5f,
            Random.Range(-4f, 4f)
        );
    }

    // 환경 관찰 수집
    public override void CollectObservations(VectorSensor sensor)
    {
        // 타겟 상대 위치 (3개 값)
        sensor.AddObservation(target.localPosition - transform.localPosition);

        // 현재 속도 (3개 값)
        sensor.AddObservation(rb.velocity);
    }

    // 행동 수행
    public override void OnActionReceived(ActionBuffers actions)
    {
        // 연속 행동 (이동)
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actions.ContinuousActions[0];
        controlSignal.z = actions.ContinuousActions[1];

        rb.AddForce(controlSignal * 10f);

        // 타겟에 도달하면 보상
        float distanceToTarget = Vector3.Distance(
            transform.localPosition,
            target.localPosition
        );

        if (distanceToTarget < 1.5f)
        {
            SetReward(1.0f);
            EndEpisode();
        }

        // 플랫폼에서 떨어지면 페널티
        if (transform.localPosition.y < 0)
        {
            SetReward(-1.0f);
            EndEpisode();
        }
    }

    // 사람이 직접 제어할 때 (테스트용)
    public override void Heuristic(in ActionBuffers actionsOut)
    {
        var continuousActions = actionsOut.ContinuousActions;
        continuousActions[0] = Input.GetAxis("Horizontal");
        continuousActions[1] = Input.GetAxis("Vertical");
    }
}
```

#### 학습 설정 (YAML)

```yaml
# config/trainer_config.yaml
behaviors:
  SimpleAgent:
    trainer_type: ppo
    hyperparameters:
      batch_size: 64
      buffer_size: 2048
      learning_rate: 3.0e-4
      beta: 0.01
      epsilon: 0.2
      lambd: 0.95
      num_epoch: 3
    network_settings:
      normalize: true
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```

### 21.3.5 Unity AI Use Cases

```
Use Case 1: NPC 행동 AI
───────────────────────
목표: 플레이어를 추적하는 똑똑한 적 AI
접근법:
1. ML-Agents로 추적 에이전트 학습
   - 관찰: 플레이어 위치, 장애물, 자신의 상태
   - 행동: 이동 방향, 속도
   - 보상: 플레이어에 가까워지면 +, 벽에 부딪히면 -
2. 학습된 모델을 Sentis로 게임에 통합
3. 결과: 규칙 기반 AI보다 자연스러운 추적 행동

Use Case 2: 동적 난이도 조절
─────────────────────────────
목표: 플레이어 실력에 맞는 난이도
접근법:
1. 플레이어 행동 데이터 수집
   - 생존 시간, 정확도, 반응 속도
2. Sentis로 분류 모델 실행
   - 입력: 최근 N게임의 통계
   - 출력: 실력 레벨 (1-5)
3. 난이도 파라미터 동적 조절
   - 적 체력, 스폰 빈도, 보상 등

Use Case 3: 프로시저럴 콘텐츠
─────────────────────────────
목표: AI로 레벨/퀘스트 자동 생성
접근법:
1. 기존 레벨 데이터로 생성 모델 학습
2. Muse로 텍스처/에셋 생성
3. ML-Agents로 생성된 레벨 테스트
   - 플레이 가능성 검증
   - 난이도 밸런스 체크
4. 통과한 레벨만 플레이어에게 제공
```

### 21.3.6 Unity AI Best Practices

```
Unity AI Best Practices
───────────────────────

1. 성능 최적화
   - Sentis: GPU 백엔드 활용
   - 추론 빈도 조절 (매 프레임 X, N프레임마다 O)
   - 모델 양자화로 크기/속도 개선

2. ML-Agents 학습 팁
   - 보상 함수 설계가 가장 중요
   - 작은 문제부터 시작 → 복잡도 점진적 증가
   - 커리큘럼 학습 활용

3. Muse 활용
   - 프로토타입 단계에서 적극 활용
   - 최종 에셋은 전문 아티스트가 다듬기
   - 생성 결과물의 저작권 확인

4. 하이브리드 접근
   - AI만으로 모든 것을 해결하지 않기
   - 전통적 게임 AI + ML AI 조합
   - 예: 길찾기는 A*, 전투 결정은 ML

5. 테스트 & 검증
   - AI 행동의 예측 불가능성 고려
   - 엣지 케이스 철저히 테스트
   - 플레이테스트로 실제 재미 검증
```

---

## 21.4 도구별 학습 로드맵

```
학습 순서 권장안
────────────────────────────────────────────────

[입문자]
    │
    ├── Kilo Code 설치 및 기본 사용
    │   └── AI Chat으로 코드 질문/생성
    │
    ├── Unity Muse Chat 활용
    │   └── Unity 개발 질문 응답
    │
    ▼
[중급자]
    │
    ├── Kilo Code Agent Mode
    │   └── 자동화된 코딩 작업
    │
    ├── Bezi 3D 디자인
    │   └── 간단한 3D UI 프로토타입
    │
    ├── Unity Muse Texture/Sprite
    │   └── 게임 에셋 생성
    │
    ▼
[고급자]
    │
    ├── Unity Sentis
    │   └── 커스텀 ML 모델 게임 통합
    │
    ├── ML-Agents
    │   └── 강화학습 에이전트 훈련
    │
    └── Bezi + Unity 파이프라인
        └── 3D 디자인 → Unity 구현
```

---

## 요약

| 도구 | 주요 용도 | 대상 사용자 |
|------|----------|------------|
| **Kilo Code** | VS Code에서 AI 코딩 | 모든 개발자 |
| **Bezi** | 3D/XR UI 디자인 | UI/UX 디자이너, XR 개발자 |
| **Unity Muse** | 게임 에셋/코드 생성 | Unity 게임 개발자 |
| **Unity Sentis** | 게임 내 AI 모델 실행 | ML 엔지니어, 게임 개발자 |
| **ML-Agents** | 게임 AI 학습 | ML 엔지니어, 게임 AI 개발자 |

**핵심 메시지**: 범용 도구(Claude Code 등)로 대부분의 작업을 하되, 게임 개발이나 3D 디자인 같은 특화 영역에서는 해당 도메인에 최적화된 도구를 활용하세요. Unity AI 도구들은 특히 게임 개발 파이프라인에 깊이 통합되어 있어, 게임 개발자라면 반드시 알아두어야 합니다.

---

*이제 전체 가이드의 학습 여정이 완성되었습니다. 기초부터 최신 도구까지, 생성형 AI의 모든 것을 다루었습니다.*
