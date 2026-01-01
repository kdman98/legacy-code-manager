# Contributing to Legacy Code Capturer

Thank you for your interest in contributing to Legacy Code Capturer! This document provides guidelines and instructions for contributing.

## Code of Conduct

Be respectful and constructive in all interactions. We're building this together to help the developer community.

## How to Contribute

### Reporting Bugs

Before creating a bug report, please check existing issues to avoid duplicates. When creating a bug report, include:

- Clear, descriptive title
- Exact steps to reproduce
- Expected vs actual behavior
- Your environment (OS, Claude Code version)
- Relevant logs or error messages

### Suggesting Enhancements

Enhancement suggestions are welcome! Please provide:

- Clear description of the feature
- Use cases and benefits
- Possible implementation approach (if you have ideas)

### Pull Requests

1. **Fork and Clone**
   ```bash
   git clone https://github.com/YOUR_USERNAME/legacy-context-capturer.git
   cd legacy-context-capturer
   ```

2. **Create a Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Changes**
   - Follow existing code patterns
   - Update documentation as needed
   - Test your changes thoroughly

4. **Commit**
   ```bash
   git commit -m "Add feature: description of your changes"
   ```

5. **Push and Submit PR**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then open a Pull Request on GitHub.

## Development Guidelines

### File Structure

```
legacy-code-capturer/
├── .claude-plugin/
│   └── marketplace.json    # Plugin marketplace configuration
├── agents/                 # AI agents (*.md)
├── commands/              # User-facing commands (*.md)
├── skills/                # Shared knowledge (*.md)
└── README.md
```

### Creating New Agents

Agents must include frontmatter:

```markdown
---
name: agent-name
description: Brief description of what this agent does
---

# Agent Name

Your agent implementation here...
```

### Creating New Commands

Commands must include frontmatter:

```markdown
---
name: command-name
description: Brief description of what this command does
---

# Command Name

Your command implementation here...
```

### Agent Design Principles

1. **Single Responsibility**: Each agent should do one thing well
2. **Clear Inputs/Outputs**: Document expected inputs and outputs
3. **Scoped Awareness**: Support both full-codebase and scoped analysis
4. **JSON Communication**: Use structured JSON for agent-to-agent communication

### Testing Your Changes

1. Install your modified plugin locally
2. Test on a real codebase (preferably a complex one)
3. Verify all commands work as expected
4. Check that generated CLAUDE.md files are useful and accurate

### Documentation

- Update README.md for user-facing changes
- Add inline documentation for complex logic
- Include examples where helpful
- Keep agent/command descriptions concise but clear

## Code Review Process

1. Maintainers will review your PR within a few days
2. Address any requested changes
3. Once approved, your PR will be merged
4. Your contribution will be acknowledged in releases

## Recognition

Contributors will be:
- Listed in release notes
- Mentioned in the project README (for significant contributions)
- Forever appreciated by the community

## Questions?

Feel free to:
- Open a discussion on GitHub
- Comment on relevant issues
- Reach out to maintainers

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
