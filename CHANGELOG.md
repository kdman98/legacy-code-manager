# Changelog

All notable changes to Legacy Code Capturer will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2025-01-05

### Changed
- Synced version with remote repository release
- Updated marketplace.json to version 1.1.0

## [1.0.1] - 2025-01-01

### Changed
- Updated marketplace.json to latest Claude Code plugin format with best practices
- Renamed repository from legacy-code-manager to legacy-context-capturer
- Updated all documentation to reflect new repository name
- Added author, homepage, repository, license, and category fields to marketplace.json
- Set strict mode to false for simplified plugin distribution

### Fixed
- Removed unimplemented command flags from documentation (--skip-interview, --output, --upstream-only, --downstream-only, --format)
- Corrected installation instructions across all documentation

## [1.0.0] - 2025-01-01

### Added
- Initial release of Legacy Code Capturer
- Core commands: `/capture`, `/analyze`, `/detect-scale`, `/interview`
- Six specialized agents:
  - `scope-resolver`: Natural language scope parsing
  - `code-analyzer`: Codebase structure analysis
  - `confusion-detector`: Identifies confusing code patterns
  - `scalability-detector`: Finds performance anti-patterns
  - `interview-agent`: Extracts tribal knowledge
  - `context-writer`: Generates CLAUDE.md files
- Natural language scoping support
- Multi-file context generation
- Support for scoped vs full-codebase analysis
- Confusion detection categories: WHAT, WHEN, HISTORY, DUPLICATE, INCONSISTENT
- Scalability issue detection (N+1 queries, missing timeouts, etc.)
- Markdown context file generation
- Skills for shared context patterns

### Features
- `/capture` command with optional natural language scope
- Dependency tracing (upstream and downstream)
- Configurable depth for dependency analysis
- Support for test file inclusion/exclusion
- Multiple output formats (CLAUDE.md, .cursor/rules, context.json)
- Incremental context updates
- Interview mode for capturing developer knowledge

### Documentation
- Comprehensive README with examples
- Detailed command documentation
- Agent specification files
- Contributing guidelines
- MIT License

## [Unreleased]

### Planned
- Support for more programming languages
- IDE integrations
- Automated context updates on git commits
- Team collaboration features
- Custom agent templates
- Performance optimizations for large codebases
- Plugin configuration file support
- Enhanced natural language query understanding

---

## Release Notes Format

### Added
New features and capabilities

### Changed
Changes to existing functionality

### Deprecated
Features that will be removed in future versions

### Removed
Features that have been removed

### Fixed
Bug fixes

### Security
Security vulnerability fixes
