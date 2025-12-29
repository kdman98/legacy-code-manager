---
name: capture
description: Run full context capture pipeline - analyzes code, detects issues, conducts interview, and generates context files. Supports natural language scoping.
---

# Context Capture - Full Pipeline

Run the complete context capture workflow to generate CLAUDE.md and other context files.

## Usage

```bash
# Full codebase
/capture

# Scoped - natural language
/capture "only codes related to MydataService.kt"
/capture "payment retry logic and its dependencies"
/capture "everything that touches the User table"
/capture "notification service and what calls it"

# Scoped - with options
/capture "PaymentService" --include-tests
/capture "auth module" --depth 3
/capture "OrderService" --skip-interview
```

## Pipeline Steps

### Step 0: Scope Resolution (if query provided)

When a natural language scope is provided, invoke @scope-resolver first:

```
User: /capture "only codes related to MydataService.kt"
                           │
                           ▼
              ┌─────────────────────────┐
              │   Scope Resolver        │
              │   - Parse query intent  │
              │   - Find anchor files   │
              │   - Trace dependencies  │
              │   - Output scope.json   │
              └───────────┬─────────────┘
                          │
                          ▼
              scope.json (filtered file list)
                          │
                          ▼
              [Continue to Step 1 with scope]
```

**Scope resolution handles:**
- File references: `"MydataService.kt"` → finds exact file + deps
- Concepts: `"payment retry logic"` → finds related files by name/content
- Entities: `"User table"` → finds entity + repository + services using it
- Direction: `"what calls X"` → traces upstream only

### Step 1: Code Analysis

Invoke @code-analyzer on the repository (or scoped files).

**Without scope:**
- Scan entire codebase structure
- Identify all services and modules

**With scope:**
- Analyze only files in `scope.json`
- Still detect patterns and dependencies within scope

Output: `structure.json`

### Step 2: Confusion Detection

Invoke @confusion-detector with structure.json (respecting scope).

- Identify confusing code patterns (WHAT, WHEN, HISTORY, DUPLICATE)
- Focus on scoped files if scope.json exists

Output: `confusion_points.json`

### Step 3: Scalability Detection

Invoke @scalability-detector with structure.json (respecting scope).

- Check for performance anti-patterns
- Flag critical issues in scoped area

Output: `scalability_report.json`

### Step 4: Developer Interview

Invoke @interview-agent with context from previous steps.

- Use scope to ask targeted questions about specific area
- "I'm focusing on MydataService and related code. Let me ask about that..."
- Skip generic questions when scope is narrow

Output: `interview_knowledge.json`

### Step 5: Context Generation

Invoke @context-writer with all previous outputs.

**Without scope:**
- Generate project-wide `CLAUDE.md`

**With scope:**
- Generate scoped file: `CLAUDE-mydata.md` or similar
- Or append to existing `CLAUDE.md` as a section

Output: `CLAUDE.md`, `.cursor/rules`, `context.json`

## Pipeline Data Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  /capture "MydataService and its dependencies"                  │
│                           │                                     │
│                           ▼                                     │
│              ┌─────────────────────────┐                        │
│              │    scope-resolver       │                        │
│              │    → scope.json         │                        │
│              └───────────┬─────────────┘                        │
│                          │                                      │
│         ┌────────────────┼────────────────┐                     │
│         │                │                │                     │
│         ▼                ▼                ▼                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │code-analyzer│  │ confusion-  │  │ scalability-│              │
│  │→structure   │  │ detector    │  │ detector    │              │
│  │  .json      │  │→confusion   │  │→scalability │              │
│  │(scoped)     │  │  _points    │  │  _report    │              │
│  └──────┬──────┘  │  .json      │  │  .json      │              │
│         │         └──────┬──────┘  └──────┬──────┘              │
│         │                │                │                     │
│         └────────────────┼────────────────┘                     │
│                          │                                      │
│                          ▼                                      │
│              ┌─────────────────────────┐                        │
│              │    interview-agent      │                        │
│              │    (scoped questions)   │                        │
│              │    → interview.json     │                        │
│              └───────────┬─────────────┘                        │
│                          │                                      │
│                          ▼                                      │
│              ┌─────────────────────────┐                        │
│              │    context-writer       │                        │
│              │    → CLAUDE.md          │                        │
│              │    (scoped section)     │                        │
│              └─────────────────────────┘                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Scope Examples

### File-based scope
```bash
/capture "MydataService.kt"
```
Analyzes MydataService + imports + callers + related models.

### Service-based scope
```bash
/capture "the payment service"
```
Finds payment-related files by name/package, analyzes that boundary.

### Feature-based scope
```bash
/capture "retry logic"
```
Searches for retry-related code across codebase, scopes to those files.

### Dependency-focused scope
```bash
/capture "what depends on UserRepository"
```
Traces upstream: everything that calls UserRepository.

### Reverse dependency scope
```bash
/capture "PaymentService and everything it calls"
```
Traces downstream: PaymentService + all its dependencies.

## Options

| Flag | Description |
|------|-------------|
| `--skip-interview` | Skip interview, use code analysis only |
| `--include-tests` | Include test files in scope |
| `--depth N` | Dependency trace depth (default: 2) |
| `--upstream-only` | Only trace callers, not dependencies |
| `--downstream-only` | Only trace dependencies, not callers |
| `--output DIR` | Custom output directory |
| `--format all` | Generate all output formats |
| `--append` | Append to existing CLAUDE.md instead of creating scoped file |

## Output

**Unscoped `/capture`:**
- `CLAUDE.md` - Full project context
- `.cursor/rules` - Cursor rules (optional)
- `.context/context.json` - Machine-readable (optional)

**Scoped `/capture "MydataService"`:**
- `CLAUDE-mydata.md` - Scoped context file
- Or `CLAUDE.md` with `## MydataService Context` section (if `--append`)
- `scope.json` - The resolved scope (for debugging/reuse)

## Incremental Updates

Re-run scoped capture to update specific sections:

```bash
# Initial full capture
/capture

# Later: update just the payment section
/capture "payment service" --append

# This updates the payment section in CLAUDE.md without re-analyzing everything
```

## Notes

- Scope resolution adds ~5-10 seconds to identify relevant files
- Interview questions are tailored to scoped area
- Scoped captures are faster and more focused
- Use unscoped `/capture` first for full context, then scoped for updates
