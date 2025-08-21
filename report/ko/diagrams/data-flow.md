# 데이터 흐름 다이어그램

이 문서는 Gemini CLI 시스템을 통해 정보가 어떻게 이동하는지를 보여주는 종합적인 데이터 흐름 다이어그램을 포함하고 있습니다.

## 전체 시스템 데이터 흐름

```mermaid
flowchart TB
    subgraph "입력 계층"
        UserInput[사용자가 명령어 입력]
        FileInput[파일 참조 @file.js]
        ConfigInput[설정 파일]
        MemoryInput[GEMINI.md 컨텍스트]
    end
    
    subgraph "처리 계층"
        Parser[입력 파서]
        ContextBuilder[컨텍스트 빌더]
        PromptConstructor[프롬프트 생성기]
        APICall[Gemini API 호출]
        ResponseHandler[응답 핸들러]
    end
    
    subgraph "도구 실행 계층"
        ToolDispatcher[도구 디스패처]
        ToolValidator[도구 검증기]
        ToolExecutor[도구 실행기]
        ResultProcessor[결과 처리기]
    end
    
    subgraph "출력 계층"
        Formatter[출력 포매터]
        UIRenderer[UI 렌더러]
        FileWriter[파일 작성기]
        StateUpdater[상태 업데이터]
    end
    
    UserInput --> Parser
    FileInput --> Parser
    ConfigInput --> ContextBuilder
    MemoryInput --> ContextBuilder
    
    Parser --> ContextBuilder
    ContextBuilder --> PromptConstructor
    PromptConstructor --> APICall
    APICall --> ResponseHandler
    
    ResponseHandler --> ToolDispatcher
    ToolDispatcher --> ToolValidator
    ToolValidator --> ToolExecutor
    ToolExecutor --> ResultProcessor
    
    ResultProcessor --> APICall
    ResponseHandler --> Formatter
    Formatter --> UIRenderer
    ToolExecutor --> FileWriter
    ResultProcessor --> StateUpdater
    
    StateUpdater --> ContextBuilder
```

## 요청-응답 사이클 데이터 흐름

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant Parser
    participant Context
    participant API
    participant Tools
    participant UI
    
    User->>CLI: 입력: "file.js를 읽고 설명해줘"
    CLI->>Parser: 원시 입력 문자열
    Parser->>Parser: @ 참조 파싱
    Parser->>Context: 파일 내용 추가
    Context->>Context: GEMINI.md와 병합
    Context->>API: 완전한 프롬프트
    
    API->>API: Gemini로 처리
    API->>Tools: 요청: read_file(file.js)
    Tools->>Tools: 검증 및 실행
    Tools->>API: 파일 내용
    API->>API: 설명 생성
    API->>UI: 최종 응답
    UI->>User: 형식화된 출력
```

## 파일 작업 데이터 흐름

```mermaid
flowchart LR
    subgraph "파일 탐색"
        UserPath[사용자 경로 입력]
        PathResolver[경로 해결기]
        GitIgnoreFilter[Git Ignore 필터]
        FileList[필터링된 파일 목록]
    end
    
    subgraph "파일 읽기"
        FileReader[파일 리더]
        ContentCache[콘텐츠 캐시]
        SizeChecker[크기 검사기]
        EncodingDetector[인코딩 탐지기]
    end
    
    subgraph "콘텐츠 처리"
        ContentParser[콘텐츠 파서]
        MetadataExtractor[메타데이터 추출기]
        TokenCounter[토큰 카운터]
        ContentOptimizer[콘텐츠 최적화기]
    end
    
    UserPath --> PathResolver
    PathResolver --> GitIgnoreFilter
    GitIgnoreFilter --> FileList
    
    FileList --> FileReader
    FileReader --> ContentCache
    FileReader --> SizeChecker
    SizeChecker --> EncodingDetector
    
    EncodingDetector --> ContentParser
    ContentParser --> MetadataExtractor
    MetadataExtractor --> TokenCounter
    TokenCounter --> ContentOptimizer
    
    ContentOptimizer --> PromptBuilder[프롬프트 빌더]
```

## 설정 데이터 흐름

```mermaid
flowchart TB
    subgraph "설정 소스"
        GlobalConfig[~/.gemini/settings.json]
        ProjectConfig[.gemini/settings.json]
        EnvVars[환경 변수]
        CLIArgs[명령행 인수]
    end
    
    subgraph "로딩 파이프라인"
        FileLoader[파일 로더]
        EnvLoader[환경 변수 로더]
        ArgParser[인수 파서]
        Validator[설정 검증기]
    end
    
    subgraph "병합 전략"
        BaseMerger[기본 설정 병합기]
        OverrideMerger[오버라이드 병합기]
        FinalConfig[최종 설정]
    end
    
    subgraph "배포"
        UIConfig[UI 설정]
        CoreConfig[Core 설정]
        ToolConfig[도구 설정]
        MCPConfig[MCP 설정]
    end
    
    GlobalConfig --> FileLoader
    ProjectConfig --> FileLoader
    EnvVars --> EnvLoader
    CLIArgs --> ArgParser
    
    FileLoader --> Validator
    EnvLoader --> Validator
    ArgParser --> Validator
    
    Validator --> BaseMerger
    BaseMerger --> OverrideMerger
    OverrideMerger --> FinalConfig
    
    FinalConfig --> UIConfig
    FinalConfig --> CoreConfig
    FinalConfig --> ToolConfig
    FinalConfig --> MCPConfig
```

## 인증 데이터 흐름

```mermaid
flowchart TB
    subgraph "인증 트리거"
        APIRequest[API 요청 시작]
        TokenCheck[기존 토큰 확인]
        TokenExpired[토큰 만료?]
    end
    
    subgraph "OAuth 플로우"
        LaunchBrowser[브라우저 실행]
        AuthServer[인증 서버]
        CallbackServer[로컬 콜백 서버]
        TokenExchange[토큰 교환]
    end
    
    subgraph "토큰 관리"
        TokenStorage[안전한 토큰 저장소]
        TokenRefresh[토큰 새로고침 로직]
        TokenValidation[토큰 검증]
    end
    
    subgraph "API 통신"
        APIClient[Gemini API 클라이언트]
        RequestWithToken[인증된 요청]
        ResponseHandler[응답 핸들러]
    end
    
    APIRequest --> TokenCheck
    TokenCheck --> TokenExpired
    TokenExpired -->|예| LaunchBrowser
    TokenExpired -->|아니요| APIClient
    
    LaunchBrowser --> AuthServer
    AuthServer --> CallbackServer
    CallbackServer --> TokenExchange
    TokenExchange --> TokenStorage
    
    TokenStorage --> TokenRefresh
    TokenRefresh --> TokenValidation
    TokenValidation --> APIClient
    
    APIClient --> RequestWithToken
    RequestWithToken --> ResponseHandler
```

## 도구 실행 데이터 흐름

```mermaid
flowchart TB
    subgraph "도구 요청"
        ToolCall[LLM의 도구 호출]
        ParameterExtraction[매개변수 추출]
        ValidationCheck[매개변수 검증]
        PermissionCheck[권한 확인]
    end
    
    subgraph "보안 계층"
        TrustLevel[신뢰 수준 확인]
        UserConfirmation[사용자 확인]
        SandboxSetup[샌드박스 설정]
        ResourceLimits[리소스 제한]
    end
    
    subgraph "실행 환경"
        ToolLoader[도구 로더]
        ExecutionContext[실행 컨텍스트]
        ProcessMonitor[프로세스 모니터]
        ResultCapture[결과 캡처]
    end
    
    subgraph "결과 처리"
        OutputSanitizer[출력 새니타이저]
        ErrorHandler[오류 핸들러]
        ResultFormatter[결과 포매터]
        StateUpdate[상태 업데이트]
    end
    
    ToolCall --> ParameterExtraction
    ParameterExtraction --> ValidationCheck
    ValidationCheck --> PermissionCheck
    
    PermissionCheck --> TrustLevel
    TrustLevel --> UserConfirmation
    UserConfirmation --> SandboxSetup
    SandboxSetup --> ResourceLimits
    
    ResourceLimits --> ToolLoader
    ToolLoader --> ExecutionContext
    ExecutionContext --> ProcessMonitor
    ProcessMonitor --> ResultCapture
    
    ResultCapture --> OutputSanitizer
    OutputSanitizer --> ErrorHandler
    ErrorHandler --> ResultFormatter
    ResultFormatter --> StateUpdate
```

## MCP 서버 통신 흐름

```mermaid
sequenceDiagram
    participant Core
    participant MCPManager
    participant Transport
    participant MCPServer
    participant Tools
    
    Core->>MCPManager: 도구 실행 요청
    MCPManager->>MCPManager: 적절한 서버 선택
    MCPManager->>Transport: 연결 설정
    Transport->>MCPServer: 연결
    MCPServer->>Transport: 서버 기능
    Transport->>MCPManager: 연결 준비
    
    MCPManager->>Transport: 도구 호출 전송
    Transport->>MCPServer: 도구 실행
    MCPServer->>MCPServer: 요청 처리
    MCPServer->>Transport: 도구 결과
    Transport->>MCPManager: 결과 수신
    MCPManager->>Core: 결과 반환
    
    Core->>Tools: 결과 처리
    Tools->>Core: 형식화된 출력
```

## 메모리 및 컨텍스트 흐름

```mermaid
flowchart TB
    subgraph "메모리 소스"
        GlobalMemory[전역 GEMINI.md]
        ProjectMemory[프로젝트 GEMINI.md] 
        DirectoryMemory[디렉토리 GEMINI.md]
        RuntimeMemory[런타임 추가]
    end
    
    subgraph "메모리 처리"
        MemoryLoader[메모리 로더]
        HierarchyMerger[계층 병합기]
        ContextOptimizer[컨텍스트 최적화기]
        TokenLimiter[토큰 제한기]
    end
    
    subgraph "컨텍스트 조립"
        SystemInstructions[시스템 지침]
        ConversationHistory[대화 히스토리]
        CurrentInput[현재 입력]
        ToolDefinitions[도구 정의]
    end
    
    subgraph "최종 컨텍스트"
        ContextBuilder[컨텍스트 빌더]
        PromptAssembler[프롬프트 어셈블러]
        APIPayload[API 페이로드]
    end
    
    GlobalMemory --> MemoryLoader
    ProjectMemory --> MemoryLoader
    DirectoryMemory --> MemoryLoader
    RuntimeMemory --> MemoryLoader
    
    MemoryLoader --> HierarchyMerger
    HierarchyMerger --> ContextOptimizer
    ContextOptimizer --> TokenLimiter
    
    TokenLimiter --> SystemInstructions
    SystemInstructions --> ContextBuilder
    ConversationHistory --> ContextBuilder
    CurrentInput --> ContextBuilder
    ToolDefinitions --> ContextBuilder
    
    ContextBuilder --> PromptAssembler
    PromptAssembler --> APIPayload
```

## 스트리밍 응답 데이터 흐름

```mermaid
flowchart LR
    subgraph "API 응답"
        StreamingAPI[스트리밍 API 응답]
        ChunkReceiver[청크 수신기]
        ChunkParser[청크 파서]
    end
    
    subgraph "처리 파이프라인"
        ContentProcessor[콘텐츠 처리기]
        ToolCallExtractor[도구 호출 추출기]
        TextAccumulator[텍스트 누적기]
    end
    
    subgraph "UI 업데이트"
        UIUpdater[UI 업데이터]
        ProgressIndicator[진행률 표시기]
        ContentRenderer[콘텐츠 렌더러]
    end
    
    subgraph "도구 통합"
        ToolQueue[도구 실행 큐]
        ToolProcessor[도구 처리기]
        ToolResults[도구 결과]
    end
    
    StreamingAPI --> ChunkReceiver
    ChunkReceiver --> ChunkParser
    ChunkParser --> ContentProcessor
    
    ContentProcessor --> ToolCallExtractor
    ContentProcessor --> TextAccumulator
    ToolCallExtractor --> ToolQueue
    TextAccumulator --> UIUpdater
    
    UIUpdater --> ProgressIndicator
    UIUpdater --> ContentRenderer
    
    ToolQueue --> ToolProcessor
    ToolProcessor --> ToolResults
    ToolResults --> ContentProcessor
```

## 오류 전파 흐름

```mermaid
flowchart TB
    subgraph "오류 소스"
        APIError[API 오류]
        ToolError[도구 오류]
        ConfigError[설정 오류]
        SystemError[시스템 오류]
    end
    
    subgraph "오류 감지"
        ErrorClassifier[오류 분류기]
        SeverityAssigner[심각도 할당기]
        ContextCollector[컨텍스트 수집기]
    end
    
    subgraph "오류 처리"
        RecoveryStrategy[복구 전략]
        RetryLogic[재시도 로직]
        FallbackHandler[대체 핸들러]
        UserNotification[사용자 알림]
    end
    
    subgraph "오류 보고"
        Logger[오류 로거]
        Telemetry[텔레메트리 수집기]
        UserFeedback[사용자 피드백]
    end
    
    APIError --> ErrorClassifier
    ToolError --> ErrorClassifier
    ConfigError --> ErrorClassifier
    SystemError --> ErrorClassifier
    
    ErrorClassifier --> SeverityAssigner
    SeverityAssigner --> ContextCollector
    ContextCollector --> RecoveryStrategy
    
    RecoveryStrategy --> RetryLogic
    RecoveryStrategy --> FallbackHandler
    RecoveryStrategy --> UserNotification
    
    RecoveryStrategy --> Logger
    Logger --> Telemetry
    UserNotification --> UserFeedback
```

## 체크포인트 및 상태 관리 흐름

```mermaid
flowchart TB
    subgraph "상태 캡처"
        StateSnapshot[상태 스냅샷]
        ConversationState[대화 상태]
        FileState[파일 상태]
        ConfigState[설정 상태]
    end
    
    subgraph "체크포인트 생성"
        StateSerializer[상태 직렬화기]
        Compressor[데이터 압축기]
        Encryptor[데이터 암호화기]
        CheckpointWriter[체크포인트 작성기]
    end
    
    subgraph "저장소 계층"
        LocalStorage[로컬 저장소]
        CloudStorage[클라우드 저장소]
        BackupStorage[백업 저장소]
    end
    
    subgraph "복구 프로세스"
        CheckpointLoader[체크포인트 로더]
        DataDecryptor[데이터 복호화기]
        StateDeserializer[상태 역직렬화기]
        StateRestorer[상태 복원기]
    end
    
    StateSnapshot --> ConversationState
    StateSnapshot --> FileState
    StateSnapshot --> ConfigState
    
    ConversationState --> StateSerializer
    FileState --> StateSerializer
    ConfigState --> StateSerializer
    
    StateSerializer --> Compressor
    Compressor --> Encryptor
    Encryptor --> CheckpointWriter
    
    CheckpointWriter --> LocalStorage
    CheckpointWriter --> CloudStorage
    CheckpointWriter --> BackupStorage
    
    LocalStorage --> CheckpointLoader
    CheckpointLoader --> DataDecryptor
    DataDecryptor --> StateDeserializer
    StateDeserializer --> StateRestorer
```

## 성능 모니터링 데이터 흐름

```mermaid
flowchart LR
    subgraph "메트릭 수집"
        APIMetrics[API 응답 시간]
        ToolMetrics[도구 실행 시간]
        UIMetrics[UI 렌더링 시간]
        MemoryMetrics[메모리 사용량]
    end
    
    subgraph "데이터 처리"
        MetricsAggregator[메트릭 집계기]
        StatisticsCalculator[통계 계산기]
        ThresholdChecker[임계값 검사기]
        TrendAnalyzer[트렌드 분석기]
    end
    
    subgraph "분석 결과"
        PerformanceReport[성능 보고서]
        AlertGenerator[알림 생성기]
        OptimizationSuggestions[최적화 제안]
    end
    
    subgraph "액션"
        AutoOptimizer[자동 최적화기]
        UserNotifications[사용자 알림]
        TelemetryReporter[텔레메트리 보고자]
    end
    
    APIMetrics --> MetricsAggregator
    ToolMetrics --> MetricsAggregator
    UIMetrics --> MetricsAggregator
    MemoryMetrics --> MetricsAggregator
    
    MetricsAggregator --> StatisticsCalculator
    StatisticsCalculator --> ThresholdChecker
    ThresholdChecker --> TrendAnalyzer
    
    TrendAnalyzer --> PerformanceReport
    TrendAnalyzer --> AlertGenerator
    TrendAnalyzer --> OptimizationSuggestions
    
    PerformanceReport --> AutoOptimizer
    AlertGenerator --> UserNotifications
    OptimizationSuggestions --> TelemetryReporter
```

이러한 데이터 흐름 다이어그램은 사용자 입력에서 최종 출력까지, 그 과정에서 발생하는 모든 처리 단계, 변환, 상태 변경을 포함하여 Gemini CLI 시스템을 통해 정보가 어떻게 이동하는지에 대한 종합적인 시각을 제공합니다.