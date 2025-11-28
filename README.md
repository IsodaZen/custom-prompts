# Custom Prompts for Claude Code

[日本語](./README.ja.md) | English

---

## Quick Start

Get started in 2 simple steps:

```bash
# 1. Install as a plugin
/plugin install custom-prompts

# 2. Use in Claude Code
/review:security
```

That's it! See [Installation](#installation) for alternative installation methods.

---

## Overview

A collection of specialized custom commands for Claude Code that streamline software development reviews. Each command focuses on specific perspectives (security, performance, technical debt, etc.) and provides flexible, project-adaptable analysis with both English and Japanese versions.

### Available Commands

#### Review Commands

| Command | Purpose | Status |
|---------|---------|--------|
| `/review:security` | Security-focused review | ✅ Available |
| `/review:after` | Post-implementation diff review | ✅ Available |
| `/review:perf` | Performance analysis | ✅ Available |
| `/review:coverage` | Test coverage and quality analysis | ✅ Available |
| `/review:prompt` | AI prompt quality evaluation | ✅ Available |
| `/review:note` | Documentation and article quality review | ✅ Available |

#### Development Workflow Commands

| Command | Purpose | Status |
|---------|---------|--------|
| `/commit` | Create git commits without auto-generated footers | ✅ Available |

#### `/review:security`

Identifies security vulnerabilities and assesses OWASP Top 10 compliance. Covers authentication, authorization, input validation, injection vulnerabilities, data protection, secrets management, and API security.

**Best for**: Authentication/authorization features, external input handling, pre-deployment checks, post-incident reviews.

---

#### `/review:after`

Evaluates completed changes for quality standards and deployment readiness. Analyzes requirements fulfillment, change impact, code quality, test coverage, and documentation.

**Best for**: Pre-PR reviews, pre-merge checks, release quality validation.

---

#### `/review:perf`

Identifies performance bottlenecks and optimizes resource usage. Analyzes algorithms, database queries, memory management, I/O operations, caching, and concurrency.

**Best for**: Performance issue investigation, large data processing features, API endpoints, production performance degradation.

---

#### `/review:coverage`

Analyzes test coverage and quality to ensure comprehensive testing. Evaluates test effectiveness, identifies coverage gaps, assesses testability, and reviews test reliability and maintainability.

**Best for**: Test quality assessment, coverage gap identification, pre-release quality checks, test strategy evaluation.

---

#### `/review:prompt`

Evaluates and improves prompts for Large Language Models. Reviews clarity, structure, context management, safety, and maintainability.

**Best for**: New prompt creation, underperforming prompts, maintainability improvements.

---

#### `/review:note`

Evaluates documentation and articles for readability, consistency, and clarity. Prioritizes reader experience by identifying misleading expressions, ambiguous descriptions, and AI-generated writing patterns. Focuses on creating content that is read and understood, rather than just comprehensive.

**Best for**: Blog posts, technical articles, documentation, tutorials, README files, release notes.

---

#### `/commit`

Creates git commits using Conventional Commits format without Claude Code's auto-generated footers. Provides clean, standardized commit messages that follow team conventions.

**Best for**: Projects with strict commit message policies, teams that prefer clean commit history, integration with automated changelog generation.

**Features**:
- Conventional Commits format: `<type>(<scope>): <description>`
- Type list provided: feat, fix, docs, style, refactor, test, chore
- No auto-generated footers (🤖 Generated with Claude Code...)

### Installation

#### Method 1: Plugin Installation (Recommended)

Install directly as a Claude Code plugin:

```bash
# Install from the plugin marketplace
/plugin install custom-prompts
```

Or install from a local clone:

```bash
# Clone the repository
git clone https://github.com/IsodaZen/custom-prompts.git

# In Claude Code, install from local directory
/plugin install /path/to/custom-prompts
```

After installation, verify with:

```bash
/help
```

All commands will be available immediately across your projects.

#### Method 2: Manual Installation

If you prefer manual installation or want to customize specific commands:

**Project-level installation** (recommended for team sharing):

```bash
# Clone the repository
git clone https://github.com/IsodaZen/custom-prompts.git
cd custom-prompts

# Copy to your project's .claude directory
cp -r commands .claude/
```

**User-level installation** (available across all projects):

```bash
# Copy to user's .claude directory
cp -r commands ~/.claude/
```

After installation, your directory structure will look like:

```
your-project/
└── .claude/
    └── commands/
        ├── commit.md
        ├── review:security.md
        ├── review:after.md
        ├── review:perf.md
        ├── review:coverage.md
        ├── review:prompt.md
        └── review:note.md
```

**Note**: Japanese versions (`commands-ja/`) are available in the repository for reference but are not required for installation.

## File Structure

```
custom-prompts/
├── .claude-plugin/       # Plugin metadata
│   └── plugin.json      # Plugin configuration
├── README.md              # This file (English)
├── README.ja.md          # Japanese version
├── CLAUDE.md             # Detailed guide for Claude Code
├── CONTRIBUTING.md       # Contribution guidelines
├── commands/             # Command files (for installation)
│   ├── commit.md
│   ├── review:security.md
│   ├── review:after.md
│   ├── review:perf.md
│   ├── review:coverage.md
│   ├── review:prompt.md
│   └── review:note.md
└── commands-ja/          # Japanese versions (for reference)
    ├── commit.ja.md
    ├── review:security.ja.md
    ├── review:after.ja.md
    ├── review:perf.ja.md
    ├── review:coverage.ja.md
    ├── review:prompt.ja.md
    └── review:note.ja.md
```

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](./CONTRIBUTING.md) for detailed guidelines on:

- Reporting bugs and suggesting features
- Creating new commands
- Improving existing commands
- Development workflow and testing

Quick contribution steps:
1. Fork this repository
2. Create a feature branch (`git checkout -b feature/amazing-prompt`)
3. Follow the guidelines in [CONTRIBUTING.md](./CONTRIBUTING.md)
4. Create a Pull Request

## FAQ

**Q: Do I need to install all commands?**

A: No, you can selectively install only the commands you need.

**Q: Do I need to install the Japanese versions (commands-ja/)?**

A: No, they are not required for installation. Install the English versions from the `commands/` directory. Japanese versions are available for reference if you want to review the command content in Japanese. Regardless of which version you use, conversations with Claude will be conducted in Japanese.

**Q: Should I use project-level or user-level installation?**

A:
- **Project-level** (`.claude/commands/`): When you want to share with your team or customize for a specific project
- **User-level** (`~/.claude/commands/`): When you want to use across all projects or for personal customization

**Q: What happens if the same command exists at both project and user levels?**

A: According to Claude Code's official specification, conflicts between user-level and project-level commands are not supported. Please install to only one location.

**Q: Do I need to restart Claude Code after modifying command files?**

A: No, command files are loaded at execution time, so no restart is needed.

**Q: Can I use the same commands across multiple projects?**

A: Yes, you have these options:
- **User-level installation**: Automatically available across all projects
- **Project-level installation**: Copy individually to each project, or manage with symbolic links

**Q: Custom commands don't appear in `/help`**

A: Please check:
- Files are placed in the correct directory (`.claude/commands/` or `~/.claude/commands/`)
- File names end with `.md` extension
- Files contain front matter (section enclosed by `---`)

### License

MIT License

### Author

[@IsodaZen](https://github.com/IsodaZen)
