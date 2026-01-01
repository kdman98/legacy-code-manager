# Legacy Code Capturer

A Claude Code plugin that captures tribal knowledge from legacy codebases and developers, generating structured context files for AI coding assistants.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-blue)](https://github.com/kdman98/legacy-context-capturer)

## The Problem

Every codebase has tribal knowledge - the undocumented gotchas, historical decisions, and "don't touch this" warnings that live only in developers' heads. When people leave or codebases grow, this knowledge is lost.

AI coding assistants like Claude work better with context, but creating and maintaining context files (CLAUDE.md, .cursor/rules) is tedious and often neglected.

## The Solution

Context Capturer uses multiple specialized agents to:

1. **Analyze** your codebase structure automatically
2. **Detect** scalability issues, confusion points, and inconsistencies
3. **Interview** developers to extract tribal knowledge
4. **Generate** structured context files

## Installation

```bash
# Add the marketplace
/plugin marketplace add kdman98/legacy-context-capturer

# Install the plugin
/plugin install legacy-code-capturer
```

## Usage

### Full Pipeline
```bash
/capture
```
Runs all agents in sequence on entire codebase.

### Scoped Pipeline (NEW)
```bash
/capture "only codes related to MydataService.kt"
/capture "payment retry logic and its dependencies"
/capture "everything that touches the User table"
```
Natural language scoping — analyze just what you need.

### Individual Commands
```bash
/analyze          # Just analyze codebase structure
/detect-scale     # Just check for scalability issues
/interview        # Just run the interview
```

## Agents

| Agent | Purpose |
|-------|---------|
| **scope-resolver** | Parses natural language scope, traces dependencies |
| **code-analyzer** | Scans repo structure, identifies services and modules |
| **confusion-detector** | Finds confusing code: WHAT, WHEN, HISTORY, DUPLICATE, INCONSISTENT |
| **scalability-detector** | Finds N+1 queries, missing circuit breakers, cost issues |
| **interview-agent** | Extracts tribal knowledge through structured questions |
| **context-writer** | Synthesizes everything into CLAUDE.md |

## Scoping

Analyze specific areas instead of the whole codebase:

```bash
# File-based
/capture "MydataService.kt"

# Service-based  
/capture "the payment service"

# Dependency-focused
/capture "what depends on UserRepository"

# Feature-based
/capture "retry logic"
```

### How Scoping Works

```
User: /capture "MydataService and its dependencies"
                           │
                           ▼
              ┌─────────────────────────┐
              │    scope-resolver       │
              │    Parse query          │
              │    Find anchor file     │
              │    Trace dependencies   │
              │    → scope.json         │
              └───────────┬─────────────┘
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
  code-analyzer    confusion-      scalability-
  (scoped)         detector        detector
                   (scoped)        (scoped)
         │                │                │
         └────────────────┼────────────────┘
                          ▼
              ┌─────────────────────────┐
              │    interview-agent      │
              │    (scoped questions)   │
              └───────────┬─────────────┘
                          ▼
              ┌─────────────────────────┐
              │    context-writer       │
              │    → CLAUDE-mydata.md   │
              └─────────────────────────┘
```

## Confusion Detection

The confusion-detector identifies 5 types of code that needs explanation:

| Category | Question It Answers | Example |
|----------|---------------------|---------|
| **WHAT** | "What is this doing?" | Complex algorithms, magic numbers, regex |
| **WHEN** | "When/in what order?" | Temporal coupling, scheduler magic |
| **HISTORY** | "Why is it like this?" | TODO/HACK comments, legacy code |
| **DUPLICATE** | "Which one is canonical?" | Similar functions, copy-paste code |
| **INCONSISTENT** | "Why is this different?" | Callbacks in async codebase, mixed patterns |

### Inconsistency Detection (NEW)

Finds code that deviates from codebase patterns:

```markdown
### 🔀 Inconsistencies

#### MydataClient.kt
Uses callbacks + raw OkHttp while rest of codebase uses coroutines + Retrofit.
**Reason**: Legacy code from before Kotlin migration.
```

## Output Files

### CLAUDE.md / CLAUDE-{scope}.md
Main context file with:
- Project/scope overview
- Service descriptions
- Gotchas and warnings
- Scalability issues
- Inconsistencies explained
- Historical decisions
- Ownership map

### .cursor/rules (optional)
Cursor-specific rules for:
- Code style
- Patterns to follow/avoid
- Pre-modification checks

### .context/context.json (optional)
Machine-readable format for programmatic access.

## Example Output

### Full Project (CLAUDE.md)

```markdown
# Project Context: payment-service

## Overview
Payment retry service for P2P transactions. Handles failed payments
with exponential backoff and compliance-aware retry logic.

## Tech Stack
- Kotlin 1.9 / Spring Boot 3.2
- PostgreSQL 15 + Redis 7
- Runs on EKS (3 pods, auto-scaling)

## Critical Knowledge

### ⚠️ PaymentRetryService.kt
Retry intervals are tuned for Stripe's rate limits.
**Do not modify without checking with @jane.**

### 🔴 FeeCalculator.kt
Hardcoded tax logic for KR market. Legacy code from acquisition.
**Owner**: @john (only person who understands it)

### ⚡ Scalability: OrderService.kt::getOrdersWithUsers
N+1 query pattern - loads users one-by-one.
**Impact**: 1000 orders = 1001 queries
**Fix**: Use batch fetch with findAllById()

### 🔀 Inconsistency: LegacyPaymentClient.kt
Uses callbacks when rest of codebase uses coroutines.
**Reason**: Pre-dates Kotlin migration, refactor blocked by compliance freeze.
```

### Scoped (CLAUDE-mydata.md)

```markdown
# Context: MydataService

> This document covers MydataService and its dependencies. 
> For full project context, see [CLAUDE.md](./CLAUDE.md).

## Files in Scope
- `MydataService.kt` (anchor)
- `MydataRepository.kt`
- `MydataController.kt`
- `MydataClient.kt`

## Critical Knowledge

### ⚠️ MydataService.kt::processWithRetry
Retry delays tuned for external API rate limits.
**Owner**: @jane

### 🔀 MydataClient.kt
Uses callbacks + raw OkHttp (rest of codebase uses coroutines + Retrofit).
**Reason**: Legacy, refactor planned Q2.
```

## Options

| Flag | Description |
|------|-------------|
| `--include-tests` | Include test files in scope |
| `--depth N` | Dependency trace depth (default: 2) |
| `--append` | Append to existing CLAUDE.md |

## Troubleshooting

### Plugin Not Found
Ensure you've added the marketplace first:
```bash
/plugin marketplace add kdman98/legacy-context-capturer
```

### Commands Not Working
Verify the plugin is installed:
```bash
/plugin list
```

### Scope Resolution Issues
If scoped capture isn't finding files:
- Use exact file names first: `/capture "MyFile.kt"`
- Check file paths are relative to repository root
- Try broader queries: `/capture "payment service"`

## Roadmap

- [ ] Support for more programming languages
- [ ] Integration with popular IDEs
- [ ] Automated context file updates on git commits
- [ ] Team collaboration features
- [ ] Custom agent templates

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Guidelines

- Follow the existing agent/command file structure
- Include frontmatter (name, description) in all markdown files
- Test your changes with real codebases
- Update documentation for new features

## Support

- **Issues**: [GitHub Issues](https://github.com/kdman98/legacy-context-capturer/issues)
- **Discussions**: [GitHub Discussions](https://github.com/kdman98/legacy-context-capturer/discussions)

## License

MIT License - see the [LICENSE](LICENSE) file for details

## Acknowledgments

Built for the Claude Code ecosystem to help teams capture and preserve critical codebase knowledge.
