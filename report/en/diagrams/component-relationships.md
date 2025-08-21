# Component Relationships Diagrams

This document contains comprehensive diagrams showing the relationships between different components in the Gemini CLI architecture.

## System-Level Component Architecture

```mermaid
graph TB
    subgraph "User Interface Layer"
        CLI[CLI Package]
        React[React Components]
        Ink[Ink Renderer]
        Themes[Theme Engine]
    end
    
    subgraph "Application Layer"
        Core[Core Package]
        Commands[Command System]
        Config[Configuration Manager]
        Auth[Authentication Manager]
    end
    
    subgraph "Integration Layer"
        Tools[Tool System]
        MCP[MCP Integration]
        Extensions[Extension System]
        Memory[Memory Manager]
    end
    
    subgraph "External Interfaces"
        Gemini[Gemini API]
        FS[File System]
        Shell[Shell Environment]
        MCPServers[MCP Servers]
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

## Package Dependency Graph

```mermaid
graph LR
    subgraph "Packages"
        CLI[packages/cli]
        Core[packages/core]
        TestUtils[packages/test-utils]
        VSCode[packages/vscode-ide-companion]
    end
    
    subgraph "External Dependencies"
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

## Tool System Component Relationships

```mermaid
graph TB
    subgraph "Tool Registry"
        Registry[Tool Registry]
        Validator[Tool Validator]
        Manager[Tool Manager]
    end
    
    subgraph "Built-in Tools"
        FileTools[File System Tools]
        ShellTools[Shell Tools]
        WebTools[Web Tools]
        MemoryTools[Memory Tools]
    end
    
    subgraph "External Tools"
        MCPTools[MCP Tools]
        CustomTools[Custom Tools]
    end
    
    subgraph "Execution Engine"
        Executor[Tool Executor]
        Scheduler[Task Scheduler]
        Monitor[Execution Monitor]
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

## MCP Server Integration Components

```mermaid
graph TB
    subgraph "MCP Core"
        Manager[MCP Manager]
        Registry[Server Registry]
        Connection[Connection Pool]
    end
    
    subgraph "Transport Layer"
        Stdio[Stdio Transport]
        HTTP[HTTP Transport]
        SSE[SSE Transport]
        WS[WebSocket Transport]
    end
    
    subgraph "Protocol Handler"
        Parser[Message Parser]
        Serializer[Message Serializer]
        Validator[Protocol Validator]
    end
    
    subgraph "Authentication"
        OAuth[OAuth Provider]
        APIKey[API Key Auth]
        Basic[Basic Auth]
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

## Authentication Flow Components

```mermaid
graph TB
    subgraph "Authentication Manager"
        AuthMgr[Auth Manager]
        Providers[Auth Providers]
        Storage[Credential Storage]
    end
    
    subgraph "OAuth Flow"
        OAuthProvider[OAuth Provider]
        TokenCache[Token Cache]
        Browser[Browser Launcher]
        Server[Local Server]
    end
    
    subgraph "API Key Flow"
        APIProvider[API Key Provider]
        EnvLoader[Environment Loader]
        ConfigLoader[Config Loader]
    end
    
    subgraph "Vertex AI Flow"
        VertexProvider[Vertex AI Provider]
        GCPAuth[GCP Auth]
        ServiceAccount[Service Account]
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

## Configuration System Components

```mermaid
graph TB
    subgraph "Configuration Sources"
        Global[Global Config]
        Project[Project Config]
        Runtime[Runtime Args]
        Env[Environment]
    end
    
    subgraph "Configuration Engine"
        Loader[Config Loader]
        Merger[Config Merger]
        Validator[Config Validator]
        Watcher[Config Watcher]
    end
    
    subgraph "Memory System"
        GeminiMD[GEMINI.md Files]
        MemoryLoader[Memory Loader]
        ContextMgr[Context Manager]
    end
    
    subgraph "Settings Management"
        Schema[Settings Schema]
        Defaults[Default Values]
        Overrides[Override Rules]
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

## Command Processing Components

```mermaid
graph TB
    subgraph "Input Processing"
        Parser[Input Parser]
        Tokenizer[Command Tokenizer]
        Router[Command Router]
    end
    
    subgraph "Command Types"
        SlashCmd[Slash Commands]
        AtCmd[File Inclusion Commands]
        ShellCmd[Shell Commands]
        CustomCmd[Custom Commands]
    end
    
    subgraph "Command Execution"
        Executor[Command Executor]
        Validator[Parameter Validator]
        Confirmer[User Confirmer]
    end
    
    subgraph "Built-in Commands"
        ChatCmd[Chat Commands]
        ConfigCmd[Config Commands]
        ToolCmd[Tool Commands]
        HelpCmd[Help Commands]
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

## UI Component Hierarchy

```mermaid
graph TB
    subgraph "App Component"
        App[App Root]
        Layout[Layout Manager]
        Router[UI Router]
    end
    
    subgraph "Main Interface"
        Chat[Chat Interface]
        Input[Input Handler]
        Output[Output Renderer]
        Status[Status Bar]
    end
    
    subgraph "Dialog Components"
        Settings[Settings Dialog]
        Theme[Theme Selector]
        Auth[Auth Dialog]
        Confirm[Confirmation Dialog]
    end
    
    subgraph "Display Components"
        Markdown[Markdown Renderer]
        Code[Code Highlighter]
        Progress[Progress Indicator]
        Error[Error Display]
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

## Memory and Context Components

```mermaid
graph TB
    subgraph "Memory Sources"
        GlobalMD[Global GEMINI.md]
        ProjectMD[Project GEMINI.md]
        DirMD[Directory GEMINI.md]
        RuntimeMem[Runtime Memory]
    end
    
    subgraph "Memory Processing"
        MemLoader[Memory Loader]
        MemMerger[Memory Merger]
        MemCache[Memory Cache]
    end
    
    subgraph "Context Management"
        ContextBuilder[Context Builder]
        TokenCounter[Token Counter]
        Compressor[Context Compressor]
        Optimizer[Context Optimizer]
    end
    
    subgraph "Conversation State"
        History[Message History]
        Checkpoints[Checkpoints]
        SessionState[Session State]
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

## Error Handling Component Network

```mermaid
graph TB
    subgraph "Error Detection"
        APIErrors[API Errors]
        ToolErrors[Tool Errors]
        ConfigErrors[Config Errors]
        SystemErrors[System Errors]
    end
    
    subgraph "Error Processing"
        Classifier[Error Classifier]
        Handler[Error Handler]
        Recovery[Recovery Manager]
        Reporter[Error Reporter]
    end
    
    subgraph "Recovery Strategies"
        Retry[Retry Logic]
        Fallback[Fallback Handlers]
        Rollback[Rollback Manager]
        Graceful[Graceful Degradation]
    end
    
    subgraph "User Interface"
        ErrorDisplay[Error Display]
        Notifications[Notifications]
        Logging[Error Logging]
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

## Data Flow Between Components

```mermaid
graph LR
    subgraph "Input Flow"
        UserInput[User Input]
        InputProcessor[Input Processor]
        CommandParser[Command Parser]
    end
    
    subgraph "Processing Flow"
        Core[Core Engine]
        PromptBuilder[Prompt Builder]
        APIClient[API Client]
        ToolExecutor[Tool Executor]
    end
    
    subgraph "Output Flow"
        ResponseProcessor[Response Processor]
        OutputFormatter[Output Formatter]
        UIRenderer[UI Renderer]
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

## Testing Component Relationships

```mermaid
graph TB
    subgraph "Test Infrastructure"
        TestUtils[Test Utils Package]
        MockFactory[Mock Factory]
        TestRenderer[Test Renderer]
        Fixtures[Test Fixtures]
    end
    
    subgraph "Unit Tests"
        CLITests[CLI Unit Tests]
        CoreTests[Core Unit Tests]
        ToolTests[Tool Unit Tests]
        MCPTests[MCP Unit Tests]
    end
    
    subgraph "Integration Tests"
        E2ETests[E2E Tests]
        MCPIntegration[MCP Integration Tests]
        ConfigTests[Config Integration Tests]
    end
    
    subgraph "Test Tools"
        Vitest[Vitest Framework]
        InkTesting[Ink Testing Library]
        MSW[MSW Mock Service]
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

## Security Component Relationships

```mermaid
graph TB
    subgraph "Security Core"
        SecurityMgr[Security Manager]
        TrustMgr[Trust Manager]
        SandboxMgr[Sandbox Manager]
    end
    
    subgraph "Authentication Security"
        CredentialStore[Credential Store]
        TokenValidator[Token Validator]
        EncryptionMgr[Encryption Manager]
    end
    
    subgraph "Execution Security"
        PermissionChecker[Permission Checker]
        CommandValidator[Command Validator]
        OutputSanitizer[Output Sanitizer]
    end
    
    subgraph "Sandboxing"
        DockerSandbox[Docker Sandbox]
        SeatbeltSandbox[Seatbelt Sandbox]
        ProcessSandbox[Process Sandbox]
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

These component relationship diagrams provide a comprehensive view of how different parts of the Gemini CLI system interact with each other, showing dependencies, data flow, and architectural boundaries.