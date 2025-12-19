# Claude Code Custom Prompts: AI Execution Guide

**Document Purpose**: This document provides execution instructions for Claude Code AI when working with custom prompt commands in this project. All information is tailored specifically for AI execution context.

**Target Audience**: Claude Code AI Assistant

---

## AI Execution Instructions

### MUST Requirements

**Language**: All interactions with users MUST be conducted in Japanese, regardless of which language version of the prompt is being used.

**Prerequisite Collection**: Before starting any review command, you MUST collect the following information from the user:
1. Review scope (specific files, directories, or components to analyze)
2. Output format preference (console output or file output with timestamp)
3. Coding standards availability (if available, request the document or URL)

**Technical Stack**: You MUST NOT guess or assume the technical stack. Always attempt automatic detection first, then ask the user if detection fails.

**No Assumptions**: You MUST NOT make assumptions about:
- Project-specific requirements
- Team conventions
- Technology choices
- Security policies

### SHOULD Requirements

**Automatic Detection**: You SHOULD attempt to detect the technical stack automatically by following the Technical Stack Detection Protocol (see below).

**Severity-Based Filtering**: When coding standards are not provided:
- You SHOULD focus on Critical and High severity issues only
- You SHOULD mention this limitation to the user
- You SHOULD recommend providing coding standards for comprehensive review

**Delegation**: When specialized concerns are identified, you SHOULD recommend appropriate specialized commands:
- Security vulnerabilities → `/review:security`
- Performance bottlenecks → `/review:perf`
- Test quality issues → `/review:coverage`
- Documentation issues → `/review:note`
- AI prompt quality → `/review:prompt`

### MUST NOT Rules

- MUST NOT hardcode specific checklists or requirements
- MUST NOT proceed with review without collecting prerequisites
- MUST NOT output in English (all outputs must be in Japanese)
- MUST NOT guess technical stack when automatic detection fails
- MUST NOT skip severity classification in review outputs

---

## Design Principles

### 1. Generality Over Specificity
- Design prompts to be adaptable to various projects
- Avoid hardcoding technology-specific checks
- Support flexible project requirements
- Adapt review depth based on available information (coding standards)

### 2. Explicit User Interaction
- Collect all necessary information upfront before starting analysis
- Offer clear choices (output format: console vs file)
- Provide severity-based filtering options
- Set clear expectations about review scope

### 3. Language Consistency
- Enforce Japanese for all user-facing outputs
- Apply regardless of prompt file language version
- Include explicit language requirements in all command prompts

### 4. Delegation to Specialized Reviews
- Separate general concerns from specialized domains
- Recommend specific commands when appropriate
- Avoid overlap between general and specialized review commands

---

## Technical Stack Detection Protocol

### Execution Order

You MUST attempt detection in this specific order:

**Step 1: Configuration Files**
Check for these files in the workspace root and infer the technology:

- `package.json` → Node.js / JavaScript / TypeScript ecosystem
- `requirements.txt` or `pyproject.toml` → Python
- `Gemfile` or `Gemfile.lock` → Ruby
- `pom.xml` or `build.gradle` → Java
- `go.mod` → Go
- `Cargo.toml` → Rust
- `composer.json` → PHP
- `.csproj` or `.sln` → C# / .NET

**Step 2: Source Directory Structure**
Examine common source directories:

- `src/`, `lib/`, `app/` - Check for file extensions and structure
- Look for framework-specific directories (e.g., `components/`, `controllers/`, `models/`)

**Step 3: README.md Technology Section**
Read README.md for explicit technology stack mentions.

**Step 4: Ask User**
If detection fails after steps 1-3, ask the user explicitly:

```
技術スタックを自動判断できませんでした。使用している技術スタックを教えてください。

例:
- Node.js + Express
- Python + Django
- Ruby on Rails
- Java + Spring Boot
- Go + Gin
```

---

## Language Enforcement Implementation

### Requirement for All Command Prompts

Every command prompt file (e.g., `review:security.md`, `review:after.md`) MUST include the following section at the beginning:

```markdown
## Language Requirements
**All interactions with the user must be conducted in Japanese, regardless of which language version of this prompt is being used.** This includes:
- Questions to gather prerequisites
- Status updates during the review
- All review outputs and findings
- Recommendations and explanations
```

### Rationale

Claude Code's system prompt is in English, so explicit language enforcement in each command prompt is necessary to ensure consistent Japanese output for Japanese users.

---

## Prompt File Language Guidelines

### File Language Rules

- **`commands/`**: All content in English (except quoted Japanese output examples)
- **`commands-ja/`**: All content in Japanese

### Important Distinction

- **Language Requirements section**: User interaction language (always Japanese)
- **Prompt file language**: Language of prompt itself (English for `commands/`, Japanese for `commands-ja/`)

### Editing Verification

When editing prompt files, verify language consistency:
- `commands/` files: No Japanese except quoted output examples (e.g., `"問題は見つかりませんでした"`)
- `commands-ja/` files: Pure Japanese throughout
- Detection: Use regex `[\u3040-\u309F\u30A0-\u30FF\u4E00-\u9FFF]+` for Japanese characters

---

## Severity Level Definitions

Use these severity levels consistently across all review outputs:

- **Critical**: Data loss, security breach, system crash, complete feature failure, authentication/authorization bypass
- **High**: Functional defects, significant performance degradation, breaking changes without migration path, logic errors affecting core functionality
- **Medium**: Maintainability issues affecting code quality, test coverage gaps in critical paths, incomplete documentation for complex logic
- **Low**: Code style inconsistencies, naming convention deviations, minor refactoring opportunities

**Note**: Some specialized commands (e.g., `/review:security`) may include additional severity levels like "Info" for observations without immediate risk.

---

## Command Prompt Structure Template

All command prompts should follow this structure:

```markdown
---
name: command:name
description: One-line description of the command
version: 1.0.0
---

# Command Title

## Language Requirements
[Language enforcement section - see above]

## Role
Define the expert role and domain expertise

## Review Objectives
What the review aims to achieve

## Prerequisites
- Designed for use with Claude Code
- Information to request from users
- Technical stack detection approach

## Review Perspectives
### 1. Perspective Name
What to evaluate

### 2. Another Perspective
What to evaluate

## Output Format
**すべてのレビュー出力は日本語で記述してください。**

Structure of review results

## Review Process
Step-by-step process

## Important Notes
Critical considerations and guidelines
```

---

## Execution Workflow

Follow this workflow for all review commands:

### 1. Initialize
- Read workspace context
- Identify the command being executed

### 2. Detect Technical Stack
- Follow the Technical Stack Detection Protocol
- Document detected or user-provided stack

### 3. Collect Prerequisites
- Ask user for review scope
- Ask user for output format preference
- Ask user for coding standards availability
- Clarify any ambiguities

### 4. Execute Review
- Analyze code according to command-specific instructions
- Apply severity classifications
- Identify patterns and issues
- Generate recommendations

### 5. Output Results
- Format according to user's preference (console or file)
- Ensure all output is in Japanese
- Include severity levels for all findings
- Provide actionable recommendations

### 6. Recommend Follow-up (if applicable)
- Suggest specialized commands for identified concerns
- Offer additional review options

---

## Git Diff Commands for Change Reviews

For commands that review changes (e.g., `/review:after`), provide these git command examples to users:

**Compare branches:**
```bash
git diff main...feature-branch
```

**Compare commits:**
```bash
git diff abc123..def456
```

**List changed files:**
```bash
git diff --name-only main...feature-branch
```

**Get commit range:**
```bash
git log --oneline main..feature-branch
```

Ask users to provide branch names or commit IDs for accurate diff analysis.

---

## Reference Documentation

For detailed information beyond AI execution instructions, refer users to:

- **User Guide**: [README.md](./README.md) - Installation, usage, FAQ, best practices
- **Developer Guide**: [CONTRIBUTING.md](./CONTRIBUTING.md) - Creating and customizing commands, contribution guidelines
- **Japanese README**: [README.ja.md](./README.ja.md) - Japanese version of user guide

---

## Version Information

This document version is tracked in Git. See commit history for changes:
```bash
git log CLAUDE.md
```

For the latest version, ensure your local repository is up to date:
```bash
git pull origin main
```

---

**Document Scope**: AI execution instructions only. For user-facing documentation, see README.md and CONTRIBUTING.md.
