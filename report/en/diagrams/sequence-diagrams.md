# Sequence Diagrams

This document contains detailed sequence diagrams showing the temporal flow of operations in Gemini CLI.

## Application Startup Sequence

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant Config
    participant Auth
    participant Core
    participant MCP
    participant UI
    
    User->>CLI: Launch gemini command
    CLI->>Config: Load configuration files
    Config->>Config: Merge global, project, and runtime config
    Config-->>CLI: Configuration loaded
    
    CLI->>Auth: Initialize authentication
    Auth->>Auth: Check stored credentials
    Auth-->>CLI: Authentication status
    
    CLI->>Core: Initialize core engine
    Core->>MCP: Initialize MCP servers
    MCP->>MCP: Connect to configured servers
    MCP-->>Core: Server connections established
    Core-->>CLI: Core engine ready
    
    CLI->>UI: Initialize user interface
    UI->>UI: Setup React/Ink components
    UI->>UI: Apply theme configuration
    UI-->>CLI: UI ready
    
    CLI-->>User: Application ready for input
```

## User Input Processing Sequence

```mermaid
sequenceDiagram
    participant User
    participant InputHandler
    participant CommandParser
    participant FileProcessor
    participant ContextBuilder
    participant PromptBuilder
    
    User->>InputHandler: Types: "@file.js explain this code"
    InputHandler->>CommandParser: Parse input string
    CommandParser->>CommandParser: Detect @ file reference
    CommandParser->>FileProcessor: Extract file reference: file.js
    
    FileProcessor->>FileProcessor: Resolve file path
    FileProcessor->>FileProcessor: Check file access permissions
    FileProcessor->>FileProcessor: Read file contents
    FileProcessor-->>CommandParser: File contents: "console.log('hello')"
    
    CommandParser-->>ContextBuilder: Command: explain this code
    CommandParser-->>ContextBuilder: File context: file.js contents
    
    ContextBuilder->>ContextBuilder: Load GEMINI.md files
    ContextBuilder->>ContextBuilder: Merge memory hierarchy
    ContextBuilder->>PromptBuilder: Complete context
    
    PromptBuilder->>PromptBuilder: Construct system prompt
    PromptBuilder->>PromptBuilder: Add tool definitions
    PromptBuilder-->>InputHandler: Final prompt ready
```

## Authentication Flow Sequence

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant AuthManager
    participant Browser
    participant AuthServer
    participant TokenStorage
    
    CLI->>AuthManager: Initiate OAuth authentication
    AuthManager->>AuthManager: Generate state parameter
    AuthManager->>AuthManager: Create authorization URL
    
    AuthManager->>Browser: Launch browser with auth URL
    Browser->>AuthServer: User navigates to auth URL
    AuthServer-->>Browser: Display login form
    
    User->>Browser: Enter credentials
    Browser->>AuthServer: Submit credentials
    AuthServer->>AuthServer: Validate credentials
    AuthServer-->>Browser: Redirect with authorization code
    
    Browser->>AuthManager: Callback with auth code
    AuthManager->>AuthServer: Exchange code for tokens
    AuthServer-->>AuthManager: Access and refresh tokens
    
    AuthManager->>TokenStorage: Store tokens securely
    TokenStorage-->>AuthManager: Tokens stored
    AuthManager-->>CLI: Authentication successful
```

## Tool Execution Sequence

```mermaid
sequenceDiagram
    participant LLM
    participant Core
    participant ToolValidator
    participant SecurityManager
    participant User
    participant ToolExecutor
    participant FileSystem
    
    LLM->>Core: Request tool: write_file(path="test.js", content="...")
    Core->>ToolValidator: Validate tool call
    ToolValidator->>ToolValidator: Check parameter schema
    ToolValidator->>ToolValidator: Validate file path
    ToolValidator-->>Core: Validation successful
    
    Core->>SecurityManager: Check permissions
    SecurityManager->>SecurityManager: Evaluate trust level
    SecurityManager->>SecurityManager: Check file write permissions
    SecurityManager-->>Core: Permission granted, confirmation required
    
    Core->>User: Request confirmation for file write
    User-->>Core: Approve action
    
    Core->>ToolExecutor: Execute tool
    ToolExecutor->>ToolExecutor: Setup execution context
    ToolExecutor->>FileSystem: Write file
    FileSystem-->>ToolExecutor: File written successfully
    
    ToolExecutor-->>Core: Tool execution result
    Core-->>LLM: Tool result: "File written to test.js"
```

## MCP Server Connection Sequence

```mermaid
sequenceDiagram
    participant CLI
    participant MCPManager
    participant Transport
    participant MCPServer
    participant ToolRegistry
    
    CLI->>MCPManager: Initialize MCP servers
    MCPManager->>MCPManager: Load server configurations
    
    loop For each configured server
        MCPManager->>Transport: Create transport (stdio/http/sse)
        Transport->>MCPServer: Establish connection
        MCPServer-->>Transport: Connection established
        
        Transport->>MCPServer: Send initialize request
        MCPServer-->>Transport: Server capabilities and info
        
        Transport->>MCPServer: Request available tools
        MCPServer-->>Transport: Tool definitions list
        
        MCPManager->>ToolRegistry: Register MCP tools
        ToolRegistry-->>MCPManager: Tools registered
    end
    
    MCPManager-->>CLI: All MCP servers initialized
```

## Streaming Response Handling Sequence

```mermaid
sequenceDiagram
    participant Core
    participant APIClient
    participant GeminiAPI
    participant StreamProcessor
    participant ToolExecutor
    participant UIRenderer
    
    Core->>APIClient: Send streaming request
    APIClient->>GeminiAPI: Initiate streaming call
    GeminiAPI-->>APIClient: Start response stream
    
    loop For each chunk
        GeminiAPI-->>APIClient: Stream chunk
        APIClient->>StreamProcessor: Process chunk
        StreamProcessor->>StreamProcessor: Parse JSON lines
        
        alt Text content chunk
            StreamProcessor->>UIRenderer: Update display with text
            UIRenderer-->>StreamProcessor: Text rendered
        else Tool call chunk
            StreamProcessor->>ToolExecutor: Execute tool call
            ToolExecutor->>ToolExecutor: Process tool request
            ToolExecutor-->>StreamProcessor: Tool result
            StreamProcessor->>APIClient: Send tool result to API
        end
    end
    
    GeminiAPI-->>APIClient: Stream complete
    APIClient-->>Core: Final response ready
```

## Error Handling and Recovery Sequence

```mermaid
sequenceDiagram
    participant Component
    participant ErrorHandler
    participant RecoveryManager
    participant NotificationSystem
    participant User
    participant Logger
    
    Component->>ErrorHandler: Error occurred
    ErrorHandler->>ErrorHandler: Classify error type
    ErrorHandler->>ErrorHandler: Determine severity
    ErrorHandler->>RecoveryManager: Request recovery strategy
    
    RecoveryManager->>RecoveryManager: Analyze recovery options
    
    alt Automatic recovery possible
        RecoveryManager->>Component: Execute recovery action
        Component-->>RecoveryManager: Recovery successful
        RecoveryManager->>Logger: Log recovery action
    else User intervention required
        RecoveryManager->>NotificationSystem: Display error to user
        NotificationSystem->>User: Show error dialog with options
        User-->>NotificationSystem: Select recovery option
        NotificationSystem->>RecoveryManager: User choice
        RecoveryManager->>Component: Execute user-selected recovery
    else Unrecoverable error
        RecoveryManager->>NotificationSystem: Display fatal error
        RecoveryManager->>Logger: Log fatal error
        RecoveryManager->>Component: Initiate shutdown
    end
```

## Configuration Hot Reload Sequence

```mermaid
sequenceDiagram
    participant FileWatcher
    participant ConfigReloader
    participant ConfigValidator
    participant ComponentManager
    participant UI
    participant User
    
    FileWatcher->>ConfigReloader: Configuration file changed
    ConfigReloader->>ConfigReloader: Load new configuration
    ConfigReloader->>ConfigValidator: Validate new config
    
    alt Configuration valid
        ConfigValidator-->>ConfigReloader: Validation successful
        ConfigReloader->>ConfigReloader: Compare with current config
        ConfigReloader->>ComponentManager: Apply configuration changes
        
        loop For each affected component
            ComponentManager->>ComponentManager: Update component config
            ComponentManager->>UI: Refresh component
        end
        
        ComponentManager-->>ConfigReloader: Changes applied
        ConfigReloader->>User: Configuration reloaded successfully
        
    else Configuration invalid
        ConfigValidator-->>ConfigReloader: Validation failed
        ConfigReloader->>User: Show validation errors
        ConfigReloader->>ConfigReloader: Keep current configuration
    end
```

## Conversation Checkpoint Sequence

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant ConversationManager
    participant StateCollector
    participant CheckpointStorage
    participant FileSystem
    
    User->>CLI: Execute /chat save important-discussion
    CLI->>ConversationManager: Create checkpoint with tag
    ConversationManager->>StateCollector: Collect current state
    
    StateCollector->>StateCollector: Gather conversation history
    StateCollector->>StateCollector: Collect tool execution results
    StateCollector->>StateCollector: Capture configuration state
    StateCollector-->>ConversationManager: Complete state snapshot
    
    ConversationManager->>ConversationManager: Serialize state data
    ConversationManager->>ConversationManager: Compress data
    ConversationManager->>CheckpointStorage: Store checkpoint
    
    CheckpointStorage->>FileSystem: Write checkpoint file
    FileSystem-->>CheckpointStorage: File written
    CheckpointStorage-->>ConversationManager: Checkpoint saved
    
    ConversationManager-->>CLI: Checkpoint created successfully
    CLI-->>User: "Conversation saved as 'important-discussion'"
```

## Memory Loading and Merging Sequence

```mermaid
sequenceDiagram
    participant MemoryManager
    participant FileLoader
    participant GlobalMemory
    participant ProjectMemory
    participant DirectoryMemory
    participant MemoryMerger
    
    MemoryManager->>FileLoader: Load memory hierarchy
    
    FileLoader->>GlobalMemory: Read ~/.gemini/GEMINI.md
    GlobalMemory-->>FileLoader: Global instructions
    
    FileLoader->>ProjectMemory: Read ./GEMINI.md
    ProjectMemory-->>FileLoader: Project instructions
    
    FileLoader->>DirectoryMemory: Read ./.gemini/GEMINI.md
    DirectoryMemory-->>FileLoader: Directory instructions
    
    FileLoader-->>MemoryManager: All memory files loaded
    
    MemoryManager->>MemoryMerger: Merge memory hierarchy
    MemoryMerger->>MemoryMerger: Apply precedence rules
    MemoryMerger->>MemoryMerger: Resolve conflicts
    MemoryMerger->>MemoryMerger: Optimize for token usage
    MemoryMerger-->>MemoryManager: Merged memory context
```

## Theme Application Sequence

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant ThemeManager
    participant ThemeLoader
    participant StyleProcessor
    participant UIComponents
    
    User->>CLI: Execute /theme dark-mode
    CLI->>ThemeManager: Apply theme: dark-mode
    ThemeManager->>ThemeLoader: Load theme definition
    ThemeLoader->>ThemeLoader: Read theme file
    ThemeLoader->>ThemeLoader: Parse theme configuration
    ThemeLoader-->>ThemeManager: Theme definition loaded
    
    ThemeManager->>StyleProcessor: Process theme styles
    StyleProcessor->>StyleProcessor: Compile color palette
    StyleProcessor->>StyleProcessor: Generate component styles
    StyleProcessor->>StyleProcessor: Create semantic tokens
    StyleProcessor-->>ThemeManager: Processed styles ready
    
    ThemeManager->>UIComponents: Apply new theme
    
    loop For each UI component
        UIComponents->>UIComponents: Update component styles
        UIComponents->>UIComponents: Re-render with new theme
    end
    
    UIComponents-->>ThemeManager: Theme applied successfully
    ThemeManager-->>CLI: Theme change complete
```

## Custom Command Execution Sequence

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant CommandParser
    participant CustomCommandLoader
    participant TemplateProcessor
    participant ShellExecutor
    participant PromptBuilder
    
    User->>CLI: Execute /git:commit
    CLI->>CommandParser: Parse custom command
    CommandParser->>CustomCommandLoader: Load command: git:commit
    CustomCommandLoader->>CustomCommandLoader: Read .gemini/commands/git/commit.toml
    CustomCommandLoader-->>CommandParser: Command definition loaded
    
    CommandParser->>TemplateProcessor: Process command template
    TemplateProcessor->>TemplateProcessor: Parse template: "!{git diff --staged}"
    TemplateProcessor->>ShellExecutor: Execute: git diff --staged
    ShellExecutor-->>TemplateProcessor: Command output
    
    TemplateProcessor->>TemplateProcessor: Substitute into template
    TemplateProcessor-->>CommandParser: Final prompt template
    
    CommandParser->>PromptBuilder: Build complete prompt
    PromptBuilder-->>CLI: Execute prompt with LLM
```

## Session Cleanup Sequence

```mermaid
sequenceDiagram
    participant SignalHandler
    participant CLI
    participant ResourceManager
    participant FileHandles
    participant NetworkConnections
    participant TempFiles
    participant ProcessManager
    
    SignalHandler->>CLI: SIGINT received
    CLI->>ResourceManager: Initiate graceful shutdown
    
    ResourceManager->>FileHandles: Close open file handles
    FileHandles-->>ResourceManager: Files closed
    
    ResourceManager->>NetworkConnections: Close network connections
    NetworkConnections-->>ResourceManager: Connections closed
    
    ResourceManager->>TempFiles: Clean up temporary files
    TempFiles-->>ResourceManager: Temp files removed
    
    ResourceManager->>ProcessManager: Terminate child processes
    ProcessManager-->>ResourceManager: Processes terminated
    
    ResourceManager-->>CLI: Cleanup complete
    CLI->>CLI: Exit gracefully
```

These sequence diagrams provide detailed views of the temporal interactions between components in Gemini CLI, showing how operations unfold over time and how different parts of the system coordinate to accomplish complex tasks.