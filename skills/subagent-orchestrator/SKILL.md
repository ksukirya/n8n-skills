# Subagent Orchestrator Skill

Expert guidance for spawning, managing, and coordinating background task subagents in Claude Code.

## Overview

Use the `Task` tool to spawn specialized subagents that work autonomously on focused tasks. Subagents can run in the background while you continue other work, then report results back.

---

## Task Tool Basics

### Spawning a Subagent

```javascript
Task({
  subagent_type: "general-purpose",  // or "Bash", "Explore", "Plan"
  description: "3-5 word summary",
  prompt: "Detailed instructions for the agent",
  run_in_background: true,           // Optional: run async
  model: "sonnet"                    // Optional: "sonnet", "opus", "haiku"
})
```

### Available Subagent Types

| Type | Use Case | Tools Available |
|------|----------|-----------------|
| `general-purpose` | Multi-step tasks, research, code | All tools |
| `Bash` | Command execution, git, terminal | Bash only |
| `Explore` | Codebase exploration, file search | Read, Glob, Grep, etc. |
| `Plan` | Architecture planning, design | Read-only tools |

### Background vs Foreground

**Foreground (default):** Blocks until complete, returns result directly
```javascript
Task({
  subagent_type: "Explore",
  description: "Find auth handlers",
  prompt: "Find all authentication-related files"
})
```

**Background:** Returns immediately with output_file path
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Research API patterns",
  prompt: "...",
  run_in_background: true
})
// Returns: { output_file: "path/to/output.txt" }
// Check later with: Read(output_file) or Bash("tail output_file")
```

---

## Subagent Templates

### 1. Research Agent

**Purpose:** Deep research on topics, technologies, or codebases.

```javascript
Task({
  subagent_type: "general-purpose",
  description: "Research [TOPIC]",
  prompt: `
## Research Agent

You are a research specialist. Your task is to thoroughly investigate and report findings.

### Research Topic
[TOPIC_DESCRIPTION]

### Research Objectives
1. [OBJECTIVE_1]
2. [OBJECTIVE_2]
3. [OBJECTIVE_3]

### Instructions
- Use WebSearch for current information
- Use Grep/Glob to find relevant code if applicable
- Read documentation and source files
- Cross-reference multiple sources

### Output Format
Provide a structured report with:

## Summary
[2-3 sentence overview]

## Key Findings
1. **Finding 1**: Description
2. **Finding 2**: Description
3. **Finding 3**: Description

## Details
[Detailed explanation of each finding]

## Sources
- [Source 1]
- [Source 2]

## Recommendations
- [Actionable recommendation 1]
- [Actionable recommendation 2]
`,
  run_in_background: true,
  model: "sonnet"
})
```

**Example Usage:**
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Research OAuth2 patterns",
  prompt: `
## Research Agent

You are a research specialist investigating OAuth2 implementation patterns.

### Research Topic
Best practices for implementing OAuth2 authentication in Node.js applications.

### Research Objectives
1. Identify common OAuth2 libraries for Node.js
2. Find security best practices
3. Document token refresh patterns

### Instructions
- Use WebSearch for current 2026 information
- Look for official documentation
- Find real-world implementation examples

### Output Format
[structured report as specified above]
`,
  run_in_background: true
})
```

---

### 2. Documentation Agent

**Purpose:** Generate, update, or review documentation.

```javascript
Task({
  subagent_type: "general-purpose",
  description: "Document [COMPONENT]",
  prompt: `
## Documentation Agent

You are a technical writer specializing in clear, comprehensive documentation.

### Documentation Task
[WHAT_TO_DOCUMENT]

### Scope
- Files/folders to document: [PATHS]
- Audience: [developers/users/admins]
- Format: [markdown/JSDoc/README]

### Instructions
1. Read all relevant source files
2. Understand the code structure and purpose
3. Document public APIs, functions, and classes
4. Include usage examples
5. Note any dependencies or requirements

### Output Format

# [Component Name]

## Overview
[What it does, why it exists]

## Installation
[How to install/setup]

## Usage
[Code examples]

## API Reference
### function_name(params)
- **Parameters:**
- **Returns:**
- **Example:**

## Configuration
[Available options]

## Troubleshooting
[Common issues and solutions]
`,
  run_in_background: true,
  model: "sonnet"
})
```

**Example Usage:**
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Document auth module",
  prompt: `
## Documentation Agent

You are a technical writer documenting the authentication module.

### Documentation Task
Create comprehensive documentation for the auth/ directory.

### Scope
- Files/folders: src/auth/**/*
- Audience: developers
- Format: markdown README

### Instructions
1. Read all files in src/auth/
2. Document each exported function
3. Include authentication flow diagrams (mermaid)
4. Add usage examples

### Output Format
[as specified above]
`,
  run_in_background: true
})
```

---

### 3. Code Review Agent

**Purpose:** Review code for quality, security, and best practices.

```javascript
Task({
  subagent_type: "general-purpose",
  description: "Review [COMPONENT]",
  prompt: `
## Code Review Agent

You are a senior engineer conducting a thorough code review.

### Review Target
[FILES_OR_DIRECTORIES_TO_REVIEW]

### Review Focus
- [ ] Code quality and readability
- [ ] Security vulnerabilities
- [ ] Performance issues
- [ ] Error handling
- [ ] Test coverage
- [ ] Documentation
- [ ] Best practices

### Instructions
1. Read all target files thoroughly
2. Check for OWASP Top 10 vulnerabilities
3. Identify code smells and anti-patterns
4. Suggest specific improvements
5. Note positive patterns worth keeping

### Output Format

## Code Review Report

### Summary
- **Files Reviewed:** [count]
- **Critical Issues:** [count]
- **Warnings:** [count]
- **Suggestions:** [count]

### Critical Issues
| File | Line | Issue | Recommendation |
|------|------|-------|----------------|
| | | | |

### Security Concerns
1. **[Issue]**: Description and fix

### Performance Issues
1. **[Issue]**: Description and fix

### Code Quality
1. **[Issue]**: Description and fix

### Positive Patterns
- [Good practice observed]

### Recommendations
1. [Prioritized recommendation]
`,
  run_in_background: true,
  model: "opus"  // Use opus for thorough review
})
```

**Example Usage:**
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Review API endpoints",
  prompt: `
## Code Review Agent

You are a senior engineer reviewing API security.

### Review Target
src/api/**/*.ts

### Review Focus
- [x] Security vulnerabilities
- [x] Input validation
- [x] Authentication/authorization
- [x] Error handling
- [ ] Performance (skip for now)

### Instructions
1. Read all API route handlers
2. Check for SQL injection, XSS, CSRF
3. Verify auth middleware usage
4. Check input sanitization

### Output Format
[as specified above]
`,
  run_in_background: true,
  model: "opus"
})
```

---

### 4. n8n Workflow Builder Agent

**Purpose:** Design and build n8n workflows using MCP tools.

```javascript
Task({
  subagent_type: "general-purpose",
  description: "Build n8n [WORKFLOW]",
  prompt: `
## n8n Workflow Builder Agent

You are an n8n automation expert. Build production-ready workflows.

### Workflow Requirements
[DESCRIPTION_OF_WHAT_WORKFLOW_SHOULD_DO]

### Trigger Type
[webhook/schedule/manual/app trigger]

### Input Data
[Expected input format/source]

### Expected Output
[What should happen when workflow runs]

### Integrations Needed
- [Service 1]
- [Service 2]

### Instructions
1. Use mcp__n8n-mcp__search_nodes to find required nodes
2. Use mcp__n8n-mcp__get_node for configuration details
3. Design workflow structure
4. Use mcp__n8n-mcp__n8n_create_workflow to build
5. Use mcp__n8n-mcp__n8n_validate_workflow to verify
6. Apply fixes with mcp__n8n-mcp__n8n_autofix_workflow if needed

### Key Patterns (from n8n-workflow-builder skill)
- Webhook data is at $json.body, NOT $json directly
- Code node must return [{ json: { ... } }]
- Use expressions: {{ $json.field }}

### Output Format

## Workflow Created

**Name:** [workflow name]
**ID:** [workflow id]
**Status:** [active/inactive]

### Nodes
| Node | Type | Purpose |
|------|------|---------|
| | | |

### Connections
[Flow description]

### Testing
[How to test the workflow]

### Notes
[Any important configuration notes]
`,
  run_in_background: true,
  model: "sonnet"
})
```

**Example Usage:**
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Build Slack notification workflow",
  prompt: `
## n8n Workflow Builder Agent

You are an n8n automation expert building a Slack notification system.

### Workflow Requirements
Send a Slack message whenever a new lead is captured via webhook.

### Trigger Type
webhook (POST)

### Input Data
{
  "name": "string",
  "email": "string",
  "company": "string"
}

### Expected Output
Slack message in #leads channel with lead details.

### Integrations Needed
- Webhook trigger
- Slack

### Instructions
[as specified above]
`,
  run_in_background: true
})
```

---

## Orchestration Patterns

### Pattern 1: Parallel Research

Spawn multiple research agents simultaneously:

```javascript
// Launch in parallel (single message with multiple Task calls)
Task({
  subagent_type: "general-purpose",
  description: "Research frontend frameworks",
  prompt: "Research React vs Vue vs Svelte for our use case...",
  run_in_background: true
})

Task({
  subagent_type: "general-purpose",
  description: "Research backend frameworks",
  prompt: "Research Express vs Fastify vs Hono...",
  run_in_background: true
})

Task({
  subagent_type: "general-purpose",
  description: "Research database options",
  prompt: "Research PostgreSQL vs MongoDB vs SQLite...",
  run_in_background: true
})

// Later: Read all output files and synthesize
```

### Pattern 2: Sequential Pipeline

Chain agents where output feeds into next:

```javascript
// Step 1: Research
const research = Task({
  subagent_type: "general-purpose",
  description: "Research auth patterns",
  prompt: "Research OAuth2 implementation patterns..."
})

// Step 2: Plan based on research
const plan = Task({
  subagent_type: "Plan",
  description: "Plan auth implementation",
  prompt: `Based on this research: ${research}
           Create implementation plan...`
})

// Step 3: Document the plan
Task({
  subagent_type: "general-purpose",
  description: "Document auth design",
  prompt: `Document this implementation plan: ${plan}`
})
```

### Pattern 3: Review and Iterate

Use code review agent, then fix issues:

```javascript
// Step 1: Review
const review = Task({
  subagent_type: "general-purpose",
  description: "Review auth module",
  prompt: "[Code Review Agent prompt]..."
})

// Step 2: Fix critical issues found
Task({
  subagent_type: "general-purpose",
  description: "Fix review issues",
  prompt: `Fix these critical issues from code review:
           ${review.criticalIssues}

           Apply fixes to the codebase.`
})
```

### Pattern 4: Background Monitoring

Long-running background tasks:

```javascript
// Start build in background
Task({
  subagent_type: "Bash",
  description: "Run full test suite",
  prompt: "Run npm test and report results",
  run_in_background: true
})

// Continue with other work...

// Check status later
Read(output_file)
// or
Bash("tail -50 " + output_file)
```

---

## Checking Background Agent Results

### Method 1: Read Output File
```javascript
Read(output_file)  // Full output
```

### Method 2: Tail Recent Output
```javascript
Bash("tail -100 " + output_file)  // Last 100 lines
```

### Method 3: TaskOutput Tool
```javascript
TaskOutput({
  task_id: "agent-id",
  block: false,  // Non-blocking check
  timeout: 5000
})
```

---

## Best Practices

### 1. Clear Prompts
- Be specific about what you want
- Include output format expectations
- Provide context and constraints

### 2. Right Agent for the Job
| Task | Best Agent Type |
|------|-----------------|
| Quick file search | `Explore` |
| Git operations | `Bash` |
| Complex research | `general-purpose` |
| Architecture design | `Plan` |

### 3. Model Selection
| Complexity | Model |
|------------|-------|
| Simple tasks | `haiku` |
| Standard tasks | `sonnet` |
| Complex analysis | `opus` |

### 4. Background vs Foreground
- **Background:** Long tasks, parallel work, non-blocking
- **Foreground:** Quick tasks, need result immediately

### 5. Result Handling
- Always check background agent results
- Parse structured output for further processing
- Handle errors gracefully

---

## Quick Reference

### Spawn Research Agent
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Research [topic]",
  prompt: "[Research Agent Template]",
  run_in_background: true
})
```

### Spawn Documentation Agent
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Document [component]",
  prompt: "[Documentation Agent Template]",
  run_in_background: true
})
```

### Spawn Code Review Agent
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Review [code]",
  prompt: "[Code Review Agent Template]",
  run_in_background: true,
  model: "opus"
})
```

### Spawn n8n Builder Agent
```javascript
Task({
  subagent_type: "general-purpose",
  description: "Build n8n [workflow]",
  prompt: "[n8n Workflow Builder Template]",
  run_in_background: true
})
```

### Check Background Agent
```javascript
Read(output_file)
// or
TaskOutput({ task_id: "id", block: false })
```
