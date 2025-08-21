# 컴포넌트 관계 다이어그램

이 문서는 Gemini CLI 아키텍처의 여러 컴포넌트 간 관계를 보여주는 종합적인 다이어그램을 포함하고 있습니다.

## 시스템 수준 컴포넌트 아키텍처

```mermaid
graph TB
    subgraph "사용자 인터페이스 계층"
        CLI[CLI 패키지]
        React[React 컴포넌트]
        Ink[Ink 렌더러]
        Themes[테마 엔진]
    end
    
    subgraph "애플리케이션 계층"
        Core[Core 패키지]
        Commands[명령어 시스템]
        Config[설정 관리자]
        Auth[인증 관리자]
    end
    
    subgraph "통합 계층"
        Tools[도구 시스템]
        MCP[MCP 통합]
        Extensions[확장 시스템]
        Memory[메모리 관리자]
    end
    
    subgraph "외부 인터페이스"
        Gemini[Gemini API]
        FS[파일 시스템]
        Shell[쉘 환경]
        MCPServers[MCP 서버]
    end
    
    CLI --> Core
    React --> Ink
    Ink --> CLI
    Themes --> React
    
    Commands --> Core
    Config --> Core
    Auth --> Core
    
    Tools --> Core
    MCP --> Core
    Extensions --> Core
    Memory --> Core
    
    Core --> Gemini
    Tools --> FS
    Tools --> Shell
    MCP --> MCPServers
```

## 패키지 의존성 그래프

```mermaid
graph LR
    subgraph "패키지"
        CLI[packages/cli]
        Core[packages/core]
        TestUtils[packages/test-utils]
        VSCode[packages/vscode-ide-companion]
    end
    
    subgraph "외부 의존성"
        React[react]
        Ink[ink]
        GeminiAPI[@google/genai]
        MCPsdk[@modelcontextprotocol/sdk]
        NodePTY[node-pty]
    end
    
    CLI --> Core
    CLI --> React
    CLI --> Ink
    CLI --> NodePTY
    
    Core --> GeminiAPI
    Core --> MCPsdk
    
    TestUtils --> CLI
    TestUtils --> Core
    
    VSCode --> Core
```

## 도구 시스템 컴포넌트 관계

```mermaid
graph TB
    subgraph "도구 레지스트리"
        Registry[도구 레지스트리]
        Validator[도구 검증기]
        Manager[도구 관리자]
    end
    
    subgraph "내장 도구"
        FileTools[파일 시스템 도구]
        ShellTools[쉘 도구]
        WebTools[웹 도구]
        MemoryTools[메모리 도구]
    end
    
    subgraph "외부 도구"
        MCPTools[MCP 도구]
        CustomTools[사용자 정의 도구]
    end
    
    subgraph "실행 엔진"
        Executor[도구 실행기]
        Scheduler[작업 스케줄러]
        Monitor[실행 모니터]
    end
    
    Registry --> Validator
    Validator --> Manager
    Manager --> Executor
    
    FileTools --> Registry
    ShellTools --> Registry
    WebTools --> Registry
    MemoryTools --> Registry
    
    MCPTools --> Registry
    CustomTools --> Registry
    
    Executor --> Scheduler
    Scheduler --> Monitor
    Monitor --> Executor
```

## MCP 서버 통합 컴포넌트

```mermaid
graph TB
    subgraph "MCP 코어"
        Manager[MCP 관리자]
        Registry[서버 레지스트리]
        Connection[연결 풀]
    end
    
    subgraph "전송 계층"
        Stdio[Stdio 전송]
        HTTP[HTTP 전송]
        SSE[SSE 전송]
        WS[WebSocket 전송]
    end
    
    subgraph "프로토콜 핸들러"
        Parser[메시지 파서]
        Serializer[메시지 직렬화기]
        Validator[프로토콜 검증기]
    end
    
    subgraph "인증"
        OAuth[OAuth 제공자]
        APIKey[API 키 인증]
        Basic[기본 인증]
    end
    
    Manager --> Registry
    Registry --> Connection
    Connection --> Stdio
    Connection --> HTTP
    Connection --> SSE
    Connection --> WS
    
    Stdio --> Parser
    HTTP --> Parser
    SSE --> Parser
    WS --> Parser
    
    Parser --> Validator
    Validator --> Serializer
    
    OAuth --> HTTP
    APIKey --> HTTP
    Basic --> HTTP
```

## 인증 플로우 컴포넌트

```mermaid
graph TB
    subgraph "인증 관리자"
        AuthMgr[인증 관리자]
        Providers[인증 제공자]
        Storage[자격증명 저장소]
    end
    
    subgraph "OAuth 플로우"
        OAuthProvider[OAuth 제공자]
        TokenCache[토큰 캐시]
        Browser[브라우저 런처]
        Server[로컬 서버]
    end
    
    subgraph "API 키 플로우"
        APIProvider[API 키 제공자]
        EnvLoader[환경 변수 로더]
        ConfigLoader[설정 로더]
    end
    
    subgraph "Vertex AI 플로우"
        VertexProvider[Vertex AI 제공자]
        GCPAuth[GCP 인증]
        ServiceAccount[서비스 계정]
    end
    
    AuthMgr --> Providers
    Providers --> Storage
    
    Providers --> OAuthProvider
    Providers --> APIProvider
    Providers --> VertexProvider
    
    OAuthProvider --> TokenCache
    OAuthProvider --> Browser
    OAuthProvider --> Server
    
    APIProvider --> EnvLoader
    APIProvider --> ConfigLoader
    
    VertexProvider --> GCPAuth
    VertexProvider --> ServiceAccount
```

## 설정 시스템 컴포넌트

```mermaid
graph TB
    subgraph "설정 소스"
        Global[전역 설정]
        Project[프로젝트 설정]
        Runtime[런타임 인수]
        Env[환경 변수]
    end
    
    subgraph "설정 엔진"
        Loader[설정 로더]
        Merger[설정 병합기]
        Validator[설정 검증기]
        Watcher[설정 감시자]
    end
    
    subgraph "메모리 시스템"
        GeminiMD[GEMINI.md 파일]
        MemoryLoader[메모리 로더]
        ContextMgr[컨텍스트 관리자]
    end
    
    subgraph "설정 관리"
        Schema[설정 스키마]
        Defaults[기본값]
        Overrides[오버라이드 규칙]
    end
    
    Global --> Loader
    Project --> Loader
    Runtime --> Loader
    Env --> Loader
    
    Loader --> Merger
    Merger --> Validator
    Validator --> Watcher
    
    GeminiMD --> MemoryLoader
    MemoryLoader --> ContextMgr
    
    Schema --> Validator
    Defaults --> Merger
    Overrides --> Merger
```

## 명령어 처리 컴포넌트

```mermaid
graph TB
    subgraph "입력 처리"
        Parser[입력 파서]
        Tokenizer[명령어 토크나이저]
        Router[명령어 라우터]
    end
    
    subgraph "명령어 유형"
        SlashCmd[슬래시 명령어]
        AtCmd[파일 포함 명령어]
        ShellCmd[쉘 명령어]
        CustomCmd[사용자 정의 명령어]
    end
    
    subgraph "명령어 실행"
        Executor[명령어 실행기]
        Validator[매개변수 검증기]
        Confirmer[사용자 확인자]
    end
    
    subgraph "내장 명령어"
        ChatCmd[채팅 명령어]
        ConfigCmd[설정 명령어]
        ToolCmd[도구 명령어]
        HelpCmd[도움말 명령어]
    end
    
    Parser --> Tokenizer
    Tokenizer --> Router
    
    Router --> SlashCmd
    Router --> AtCmd
    Router --> ShellCmd
    Router --> CustomCmd
    
    SlashCmd --> Executor
    AtCmd --> Executor
    ShellCmd --> Executor
    CustomCmd --> Executor
    
    Executor --> Validator
    Validator --> Confirmer
    
    SlashCmd --> ChatCmd
    SlashCmd --> ConfigCmd
    SlashCmd --> ToolCmd
    SlashCmd --> HelpCmd
```

## UI 컴포넌트 계층구조

```mermaid
graph TB
    subgraph "앱 컴포넌트"
        App[앱 루트]
        Layout[레이아웃 관리자]
        Router[UI 라우터]
    end
    
    subgraph "메인 인터페이스"
        Chat[채팅 인터페이스]
        Input[입력 핸들러]
        Output[출력 렌더러]
        Status[상태 표시줄]
    end
    
    subgraph "다이얼로그 컴포넌트"
        Settings[설정 다이얼로그]
        Theme[테마 선택기]
        Auth[인증 다이얼로그]
        Confirm[확인 다이얼로그]
    end
    
    subgraph "표시 컴포넌트"
        Markdown[마크다운 렌더러]
        Code[코드 하이라이터]
        Progress[진행률 표시기]
        Error[오류 표시]
    end
    
    App --> Layout
    Layout --> Router
    Router --> Chat
    Router --> Input
    Router --> Output
    Router --> Status
    
    Chat --> Settings
    Chat --> Theme
    Chat --> Auth
    Chat --> Confirm
    
    Output --> Markdown
    Output --> Code
    Output --> Progress
    Output --> Error
```

## 메모리 및 컨텍스트 컴포넌트

```mermaid
graph TB
    subgraph "메모리 소스"
        GlobalMD[전역 GEMINI.md]
        ProjectMD[프로젝트 GEMINI.md]
        DirMD[디렉토리 GEMINI.md]
        RuntimeMem[런타임 메모리]
    end
    
    subgraph "메모리 처리"
        MemLoader[메모리 로더]
        MemMerger[메모리 병합기]
        MemCache[메모리 캐시]
    end
    
    subgraph "컨텍스트 관리"
        ContextBuilder[컨텍스트 빌더]
        TokenCounter[토큰 카운터]
        Compressor[컨텍스트 압축기]
        Optimizer[컨텍스트 최적화기]
    end
    
    subgraph "대화 상태"
        History[메시지 히스토리]
        Checkpoints[체크포인트]
        SessionState[세션 상태]
    end
    
    GlobalMD --> MemLoader
    ProjectMD --> MemLoader
    DirMD --> MemLoader
    RuntimeMem --> MemLoader
    
    MemLoader --> MemMerger
    MemMerger --> MemCache
    
    MemCache --> ContextBuilder
    ContextBuilder --> TokenCounter
    TokenCounter --> Compressor
    Compressor --> Optimizer
    
    History --> ContextBuilder
    Checkpoints --> ContextBuilder
    SessionState --> ContextBuilder
```

## 오류 처리 컴포넌트 네트워크

```mermaid
graph TB
    subgraph "오류 감지"
        APIErrors[API 오류]
        ToolErrors[도구 오류]
        ConfigErrors[설정 오류]
        SystemErrors[시스템 오류]
    end
    
    subgraph "오류 처리"
        Classifier[오류 분류기]
        Handler[오류 핸들러]
        Recovery[복구 관리자]
        Reporter[오류 보고자]
    end
    
    subgraph "복구 전략"
        Retry[재시도 로직]
        Fallback[대체 핸들러]
        Rollback[롤백 관리자]
        Graceful[우아한 성능 저하]
    end
    
    subgraph "사용자 인터페이스"
        ErrorDisplay[오류 표시]
        Notifications[알림]
        Logging[오류 로깅]
    end
    
    APIErrors --> Classifier
    ToolErrors --> Classifier
    ConfigErrors --> Classifier
    SystemErrors --> Classifier
    
    Classifier --> Handler
    Handler --> Recovery
    Recovery --> Reporter
    
    Recovery --> Retry
    Recovery --> Fallback
    Recovery --> Rollback
    Recovery --> Graceful
    
    Reporter --> ErrorDisplay
    Reporter --> Notifications
    Reporter --> Logging
```

## 컴포넌트 간 데이터 흐름

```mermaid
graph LR
    subgraph "입력 흐름"
        UserInput[사용자 입력]
        InputProcessor[입력 처리기]
        CommandParser[명령어 파서]
    end
    
    subgraph "처리 흐름"
        Core[코어 엔진]
        PromptBuilder[프롬프트 빌더]
        APIClient[API 클라이언트]
        ToolExecutor[도구 실행기]
    end
    
    subgraph "출력 흐름"
        ResponseProcessor[응답 처리기]
        OutputFormatter[출력 포매터]
        UIRenderer[UI 렌더러]
    end
    
    UserInput --> InputProcessor
    InputProcessor --> CommandParser
    CommandParser --> Core
    
    Core --> PromptBuilder
    PromptBuilder --> APIClient
    APIClient --> ToolExecutor
    ToolExecutor --> Core
    
    Core --> ResponseProcessor
    ResponseProcessor --> OutputFormatter
    OutputFormatter --> UIRenderer
```

## 테스트 컴포넌트 관계

```mermaid
graph TB
    subgraph "테스트 인프라"
        TestUtils[테스트 유틸리티 패키지]
        MockFactory[목 팩토리]
        TestRenderer[테스트 렌더러]
        Fixtures[테스트 픽스처]
    end
    
    subgraph "단위 테스트"
        CLITests[CLI 단위 테스트]
        CoreTests[Core 단위 테스트]
        ToolTests[도구 단위 테스트]
        MCPTests[MCP 단위 테스트]
    end
    
    subgraph "통합 테스트"
        E2ETests[E2E 테스트]
        MCPIntegration[MCP 통합 테스트]
        ConfigTests[설정 통합 테스트]
    end
    
    subgraph "테스트 도구"
        Vitest[Vitest 프레임워크]
        InkTesting[Ink 테스팅 라이브러리]
        MSW[MSW 목 서비스]
    end
    
    TestUtils --> MockFactory
    TestUtils --> TestRenderer
    TestUtils --> Fixtures
    
    CLITests --> TestUtils
    CoreTests --> TestUtils
    ToolTests --> TestUtils
    MCPTests --> TestUtils
    
    E2ETests --> TestUtils
    MCPIntegration --> TestUtils
    ConfigTests --> TestUtils
    
    TestUtils --> Vitest
    TestRenderer --> InkTesting
    MockFactory --> MSW
```

## 보안 컴포넌트 관계

```mermaid
graph TB
    subgraph "보안 코어"
        SecurityMgr[보안 관리자]
        TrustMgr[신뢰 관리자]
        SandboxMgr[샌드박스 관리자]
    end
    
    subgraph "인증 보안"
        CredentialStore[자격증명 저장소]
        TokenValidator[토큰 검증기]
        EncryptionMgr[암호화 관리자]
    end
    
    subgraph "실행 보안"
        PermissionChecker[권한 검사기]
        CommandValidator[명령어 검증기]
        OutputSanitizer[출력 새니타이저]
    end
    
    subgraph "샌드박싱"
        DockerSandbox[Docker 샌드박스]
        SeatbeltSandbox[Seatbelt 샌드박스]
        ProcessSandbox[프로세스 샌드박스]
    end
    
    SecurityMgr --> TrustMgr
    SecurityMgr --> SandboxMgr
    
    TrustMgr --> CredentialStore
    TrustMgr --> TokenValidator
    TrustMgr --> EncryptionMgr
    
    SecurityMgr --> PermissionChecker
    SecurityMgr --> CommandValidator
    SecurityMgr --> OutputSanitizer
    
    SandboxMgr --> DockerSandbox
    SandboxMgr --> SeatbeltSandbox
    SandboxMgr --> ProcessSandbox
```

이러한 컴포넌트 관계 다이어그램은 Gemini CLI 시스템의 여러 부분이 서로 상호작용하는 방식에 대한 종합적인 시각을 제공하며, 의존성, 데이터 흐름, 아키텍처 경계를 보여줍니다.