# Claude Code Hooks: Comprehensive Technical Report

## Executive Summary

Claude Code hooks are powerful automation points that allow users to extend and customize Claude's behavior by executing shell commands at specific moments during code generation and interaction. This system enables workflow customization, validation enforcement, and integration with external tools while maintaining user control over the execution environment.

## Table of Contents

1. [Overview](#overview)
2. [Hook Architecture](#hook-architecture)
3. [Hook Types and Events](#hook-types-and-events)
4. [Configuration System](#configuration-system)
5. [Practical Examples](#practical-examples)
6. [Security Considerations](#security-considerations)
7. [Best Practices](#best-practices)
8. [Advanced Use Cases](#advanced-use-cases)
9. [Troubleshooting](#troubleshooting)

## Overview

### What Are Hooks?

Hooks in Claude Code are configurable interception points that allow users to:
- Execute custom scripts before or after tool operations
- Validate inputs and outputs
- Enforce coding standards and policies
- Integrate with external systems
- Add contextual information to conversations
- Block or modify tool usage based on custom criteria

### Key Benefits

1. **Workflow Automation**: Automate repetitive tasks and enforce team standards
2. **Quality Control**: Validate code changes before they're written
3. **Security Enhancement**: Add additional security checks and validations
4. **Integration**: Connect Claude Code with external tools and services
5. **Customization**: Tailor Claude's behavior to specific project needs

## Hook Architecture

### System Flow

```
User Input → Claude Code → Hook Matcher → Hook Execution → Tool Execution → Post-Hook → Response
```

### Components

1. **Event System**: Monitors Claude Code lifecycle events
2. **Matcher Engine**: Determines which hooks to trigger based on patterns
3. **Executor**: Runs configured shell commands
4. **Context Manager**: Provides environment variables and context to hooks
5. **Response Handler**: Processes hook outputs and integrates feedback

## Hook Types and Events

### Event Categories

#### 1. Tool Lifecycle Events

**PreToolUse**
- Triggered: Before any tool execution
- Purpose: Validation, preparation, policy enforcement
- Can block: Yes (non-zero exit code blocks execution)

**PostToolUse**
- Triggered: After tool completion
- Purpose: Cleanup, logging, post-processing
- Can block: No (informational only)

#### 2. Session Events

**SessionStart**
- Triggered: When a new Claude Code session begins
- Purpose: Initialize environment, load configurations
- Use cases: Setup workspace, load project context

**SessionEnd**
- Triggered: When session terminates
- Purpose: Cleanup, save state, archive logs
- Use cases: Backup changes, cleanup temporary files

#### 3. Interaction Events

**UserPromptSubmit**
- Triggered: When user submits a prompt
- Purpose: Prompt validation, context injection
- Special feature: Can modify prompt via stdout

**Stop / SubagentStop**
- Triggered: When Claude or subagent completes response
- Purpose: Response processing, metric collection
- Use cases: Log interactions, analyze patterns

#### 4. System Events

**PreCompact**
- Triggered: Before context compaction
- Purpose: Save important context, optimize memory
- Use cases: Preserve critical information

**Notification**
- Triggered: On system notifications
- Purpose: React to system state changes
- Use cases: Alert handling, status updates

### Hook Event Matrix

| Event | Can Block | Receives Context | Can Modify | Primary Use Case |
|-------|-----------|------------------|------------|------------------|
| PreToolUse | ✓ | Tool details | ✓ (via blocking) | Validation |
| PostToolUse | ✗ | Tool + result | ✗ | Logging |
| UserPromptSubmit | ✓ | Prompt text | ✓ (stdout) | Context injection |
| SessionStart | ✗ | Session info | ✗ | Initialization |
| SessionEnd | ✗ | Session summary | ✗ | Cleanup |
| Stop | ✗ | Response | ✗ | Analysis |
| PreCompact | ✗ | Memory state | ✗ | Context preservation |

## Configuration System

### Configuration File Location

```bash
# Global configuration
~/.claude/settings.json

# Project-specific (if supported)
.claude/settings.json
```

### Configuration Structure

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "regex_pattern",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/script.sh",
            "env": {
              "CUSTOM_VAR": "value"
            }
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "logger 'Tool executed: $TOOL_NAME'"
          }
        ]
      }
    ]
  }
}
```

### Matcher Patterns

Matchers use regex patterns to determine when hooks trigger:

```json
{
  "matcher": "Write|Edit",  // Matches Write or Edit tools
  "matcher": "^Bash$",      // Exact match for Bash tool
  "matcher": ".*",          // Matches all tools
  "matcher": "(?i)delete",  // Case-insensitive match
}
```

## Practical Examples

### Example 1: Git Pre-commit Validation

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "#!/bin/bash\nif [[ \"$TOOL_FILE_PATH\" =~ \\.(js|ts|jsx|tsx)$ ]]; then\n  npx eslint --no-ignore \"$TOOL_FILE_PATH\"\nfi"
          }
        ]
      }
    ]
  }
}
```

### Example 2: Security Scanner

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/local/bin/security-scan.sh \"$TOOL_COMMAND\""
          }
        ]
      }
    ]
  }
}
```

### Example 3: Automatic Backup

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "cp \"$TOOL_FILE_PATH\" \"$TOOL_FILE_PATH.backup\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

### Example 4: Context Injection

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'PROJECT_CONTEXT: Using Node.js 20, TypeScript 5.3, React 18'"
          }
        ]
      }
    ]
  }
}
```

### Example 5: Performance Monitoring

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"$(date +%s)\" > /tmp/claude_tool_start"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Tool $TOOL_NAME took $(($(date +%s) - $(cat /tmp/claude_tool_start))) seconds\""
          }
        ]
      }
    ]
  }
}
```

## Security Considerations

### Critical Security Points

1. **Arbitrary Code Execution**: Hooks execute shell commands with user privileges
2. **Input Validation**: Always validate and sanitize inputs in hook scripts
3. **Path Security**: Use absolute paths to prevent PATH hijacking
4. **Secrets Management**: Never hardcode sensitive information in hooks
5. **Permission Control**: Ensure hook scripts have appropriate permissions

### Security Best Practices

```bash
# Good: Validate input
if [[ "$TOOL_FILE_PATH" =~ ^/safe/directory/ ]]; then
  # Process file
fi

# Good: Quote variables
command="$TOOL_COMMAND"

# Good: Use absolute paths
/usr/bin/git status

# Bad: Direct execution without validation
eval "$USER_INPUT"

# Bad: Unquoted variables
rm $FILE_PATH
```

### Sandboxing Recommendations

1. Run hooks in restricted shells when possible
2. Use containers for isolation
3. Implement timeout mechanisms
4. Log all hook executions for audit

## Best Practices

### 1. Hook Design Principles

- **Single Responsibility**: Each hook should do one thing well
- **Fast Execution**: Hooks should complete quickly (< 1 second ideal)
- **Idempotent**: Hooks should be safe to run multiple times
- **Error Handling**: Always handle errors gracefully

### 2. Configuration Management

```bash
# Version control your hooks
git add ~/.claude/settings.json

# Test hooks before deployment
./test-hook.sh

# Document hook behavior
# Hook: validate-typescript
# Purpose: Ensures TypeScript files compile
# Blocks: Yes (on compilation failure)
```

### 3. Performance Optimization

- Cache expensive operations
- Use background processes for non-blocking tasks
- Implement circuit breakers for external services
- Monitor hook execution times

### 4. Debugging Hooks

```bash
# Enable verbose logging
export CLAUDE_HOOK_DEBUG=1

# Test hook isolation
TOOL_NAME="Write" TOOL_FILE_PATH="/test/file.js" ./my-hook.sh

# Log to file for analysis
echo "$(date): Hook executed for $TOOL_NAME" >> ~/.claude/hook.log
```

## Advanced Use Cases

### 1. CI/CD Integration

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "if [[ \"$TOOL_COMMAND\" =~ ^git\\ push ]]; then\n  curl -X POST https://ci.example.com/trigger\nfi"
          }
        ]
      }
    ]
  }
}
```

### 2. Code Review Automation

```bash
#!/bin/bash
# pre-write-hook.sh
if [[ "$TOOL_NAME" == "Write" ]]; then
  # Check if file needs review
  if [[ "$TOOL_FILE_PATH" =~ critical/ ]]; then
    echo "CRITICAL FILE MODIFICATION - Requires review"
    # Send notification to team
    slack-notify "Critical file change: $TOOL_FILE_PATH"
  fi
fi
```

### 3. Compliance Enforcement

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "/opt/compliance/check-license-headers.sh \"$TOOL_FILE_PATH\""
          }
        ]
      }
    ]
  }
}
```

### 4. Dynamic Context Loading

```bash
#!/bin/bash
# load-project-context.sh
if [[ -f .claude-context ]]; then
  cat .claude-context
  echo "---"
  echo "Current branch: $(git branch --show-current)"
  echo "Last commit: $(git log -1 --oneline)"
fi
```

## Troubleshooting

### Common Issues and Solutions

#### Hook Not Triggering

**Symptoms**: Expected hook doesn't execute
**Solutions**:
1. Verify matcher pattern: `echo "ToolName" | grep -E "pattern"`
2. Check configuration syntax: `jq . ~/.claude/settings.json`
3. Ensure hook script is executable: `chmod +x hook.sh`
4. Review Claude Code logs for errors

#### Hook Blocking Unexpectedly

**Symptoms**: Tools fail with hook errors
**Solutions**:
1. Check exit codes: Ensure scripts exit 0 on success
2. Add error handling: `|| exit 0` for non-critical failures
3. Test scripts independently
4. Add timeout handling

#### Performance Issues

**Symptoms**: Slow tool execution
**Solutions**:
1. Profile hook execution time
2. Move heavy operations to background
3. Implement caching mechanisms
4. Use async processing where possible

### Debugging Checklist

- [ ] Configuration file is valid JSON
- [ ] Matcher patterns are correct regex
- [ ] Hook scripts have execute permissions
- [ ] Path variables are properly quoted
- [ ] Exit codes are appropriate
- [ ] Environment variables are available
- [ ] No infinite loops or recursion
- [ ] Proper error handling in place

## Environment Variables

Hooks receive contextual information through environment variables:

| Variable | Description | Available In |
|----------|-------------|--------------|
| `TOOL_NAME` | Name of the tool being executed | PreToolUse, PostToolUse |
| `TOOL_FILE_PATH` | File path for file operations | Write, Edit, Read tools |
| `TOOL_COMMAND` | Command for Bash tool | Bash tool hooks |
| `TOOL_RESULT` | Result of tool execution | PostToolUse |
| `SESSION_ID` | Current session identifier | All hooks |
| `USER_PROMPT` | User's input text | UserPromptSubmit |
| `CLAUDE_RESPONSE` | Claude's response | Stop, SubagentStop |

## Conclusion

Claude Code hooks provide a powerful extension mechanism for customizing and enhancing the development workflow. By understanding the event system, implementing security best practices, and following configuration guidelines, users can create sophisticated automation that improves productivity while maintaining code quality and security standards.

The hook system's flexibility allows for integration with virtually any external tool or service, making Claude Code adaptable to diverse development environments and team requirements. As the system evolves, hooks will continue to be a critical component for users who need to extend Claude Code's capabilities beyond its default behavior.

---

*Last Updated: January 2025*
*Documentation Version: 1.0*
*Claude Code Compatibility: Latest*