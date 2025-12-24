# Archo Subagents

A specialized subagent system for Cursor IDE, leveraging the new subagent feature available in nightly/early access builds. This system provides a team of specialized AI agents orchestrated to handle complex development workflows.

## Overview

This project defines a complete orchestration system with:
- **7 Specialized Subagents** - Each optimized for specific tasks
- **Orchestrator Command** - Enables intelligent agent routing
- **Oracle Prompting Guide** - Optimizes deep reasoning performance

The orchestrator acts as the central coordinator, delegating work to specialized agents based on task type, ensuring you always get the right expert for each job.

After installed, the orchestrator is opt-in and activated using the /orchestrator command.

## Subagents

| Agent | Model | Purpose |
|-------|-------|---------|
| **Oracle** | GPT-5.2 High | Deep reasoning, complex debugging, and strategic planning. Always use for debugging and creating plans. |
| **Designer** | Claude 4.5 Opus | UI/UX design and frontend implementation. Creates beautiful, accessible interfaces. |
| **Implementor** | GPT-5.2 Fast | Precise code implementation. Pass it a plan and phase number for exact execution. |
| **Researcher** | Gemini 3 Flash | External documentation and web research. Gathers accurate, sourced information. |
| **Search** | Gemini 3 Flash | Codebase exploration including `node_modules`. Finds code without polluting context. |
| **Reviewer** | GPT-5.2 High | Implementation fidelity review. Verifies code matches the original plan. |
| **Reviewer2** | Claude 4.5 Opus | Bug detection and code quality. Finds security issues, regressions, and logic errors. |

## Key Features

### Orchestrator Mode
The orchestrator command (`/.cursor/commands/orchestrator.md`) enables a powerful workflow where:
- **Debugging** → Always routes to Oracle first for deep analysis
- **Planning** → Oracle writes all plans; orchestrator never plans directly
- **Frontend** → Designer agent handles all UI work
- **Backend** → Implementor executes plan phases precisely
- **Research** → Researcher handles all external documentation lookups
- **Code Search** → Search agent prevents context pollution from extensive grepping

### Iterative Review Cycle
After large implementations, the system runs an **iterative review-fix loop**:
1. Both Reviewer and Reviewer2 run in parallel
2. Issues are counted (regardless of severity)
3. If total issues > 3, all issues are fixed
4. Loop continues until ≤ 3 issues remain (max 5 iterations)

### Agent Delegation Policies
Each agent has explicit policies about which other agents it can delegate to, preventing unbounded recursion and context pollution:
- Oracle can delegate to: `researcher`, `search`
- Designer can delegate to: `researcher`, `search`
- Implementor can delegate to: `researcher`, `search`
- Researcher and Search: No delegation (leaf nodes)
- Reviewers can delegate to: `researcher`, `search`

## Installation

### Per-Project Installation
Copy the `.cursor` folder into your project's root directory:

```bash
cp -r .cursor /path/to/your/project/
```

### Global Installation
Copy the `.cursor` folder to your home directory for system-wide availability:

```bash
cp -r .cursor ~/
```

## Usage

### Activating Orchestrator Mode
Use the /orchestrator command in Cursor to enable the full agent routing system. The orchestrator will:
1. Analyze your request
2. Route to the appropriate specialized agent(s)
3. Coordinate multi-agent workflows
4. Run post-implementation reviews

### Example Workflows

**Debugging an Issue:**
```
User: "Our checkout test fails intermittently in CI"
→ Orchestrator reads Oracle Prompting Guide
→ Launches Oracle with logs and context
→ Oracle returns structured debugging plan
```

**Implementing a Feature:**
```
User: "Implement phase 2 of the authentication plan"
→ Orchestrator launches Implementor with full plan + phase 2
→ Implementor executes precisely
→ Orchestrator runs Reviewer + Reviewer2 in parallel
→ Fixes any issues found
→ Repeats until ≤3 issues remain
```

**Researching an API:**
```
User: "How do I integrate Stripe subscriptions?"
→ Orchestrator launches Researcher
→ Researcher searches official docs, cross-references sources
→ Returns structured guidance with citations
```

## File Structure

```
.cursor/
├── agents/
│   ├── designer.md      # UI/UX specialist
│   ├── implementor.md   # Code execution
│   ├── oracle.md        # Deep reasoning
│   ├── researcher.md    # External research
│   ├── reviewer.md      # Plan compliance
│   ├── reviewer2.md     # Bug detection
│   └── search.md        # Codebase search
├── commands/
│   └── orchestrator.md  # Orchestrator activation
└── skills/
    └── oracle-prompting-guide/
        └── SKILL.md     # GPT-5.2 prompting optimization
```

## Why Commands Over Rules/Skills?

Commands were found to be more reliable than rules or skills for activating the orchestrator mode. Commands provide:
- Explicit activation (not always-on)
- Consistent behavior across sessions
- Clear entry point for the workflow

## Requirements

- Cursor IDE (Nightly or Early Access build with subagent support)
- Access to the models specified in agent definitions (or modify to use available models)

## Customization

Each agent definition in `.cursor/agents/` can be customized:
- **model**: Change to any supported model
- **description**: Modify when the agent is triggered
- **readonly**: Set to `true` for analysis-only agents
- System prompts can be adjusted to match your team's conventions

## License

MIT
