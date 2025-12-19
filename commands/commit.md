---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*)
description: Create a git commit
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`

## Your task

Based on the above changes, create a single git commit.

* You have the capability to call multiple tools in a single response.
* Stage and create the commit using a single message.

## Requirements
* Do not use any other tools or do anything else.
* Do not send any other text or messages besides these tool calls.
* **No auto-generated footers** - Don't add Claude Code auto-generated footers to commit messages
* Commit messages must use the language of CLAUDE.md

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
