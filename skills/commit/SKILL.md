---
name: commit
description: Create a clean, conventional-commits-formatted git commit from the current staged and unstaged changes
license: MIT
metadata:
  author: IsodaZen
  version: "1.0.0"
---

## Context

Before creating the commit, gather the current repository state by running:
- `git status`
- `git diff HEAD` (staged and unstaged changes)
- `git branch --show-current`

## Your task

Based on the above changes, create a single git commit.

- You have the capability to call multiple tools in a single response.
- Stage and create the commit using a single message.

## Requirements

- Do not use any other tools or do anything else.
- Do not send any other text or messages besides these tool calls.
- **No auto-generated footers** - Don't add Claude Code auto-generated footers to commit messages
- Commit messages must use the language of CLAUDE.md
- Commit messages should be concise, ideally 50-80 characters in a single line

## Commit Messages

### Conventional Commits Format

```
<type>(<scope>): <description>
```

### Type List

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (no functional impact)
- `refactor`: Refactoring
- `test`: Test addition/modification
- `chore`: Build/configuration changes
