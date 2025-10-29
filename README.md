# Custom Prompts for Claude Code

[日本語](./README.ja.md) | English

---

## Quick Start

Get started in 3 simple steps:

```bash
# 1. Clone the repository
git clone https://github.com/IsodaZen/custom-prompts.git

# 2. Copy commands to your project
cd your-project
cp -r /path/to/custom-prompts/commands .claude/

# 3. Use in Claude Code
/review:security
```

That's it! See [Installation](#installation) for more options.

---

## Overview

A collection of specialized custom commands for Claude Code that streamline software development reviews. Each command focuses on specific perspectives (security, performance, technical debt, etc.) and provides flexible, project-adaptable analysis with both English and Japanese versions.

### Available Commands

| Command | Purpose | Status |
|---------|---------|--------|
| `/review:security` | Security-focused review | ✅ Available |
| `/review:after` | Post-implementation diff review | ✅ Available |
| `/review:perf` | Performance analysis | ✅ Available |
| `/review:prompt` | AI prompt quality evaluation | ✅ Available |
| `/review:note` | Documentation and article quality review | ✅ Available |

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

#### `/review:prompt`

Evaluates and improves prompts for Large Language Models. Reviews clarity, structure, context management, safety, and maintainability.

**Best for**: New prompt creation, underperforming prompts, maintainability improvements.

---

#### `/review:note`

Evaluates documentation and articles for readability, consistency, and clarity. Prioritizes reader experience by identifying misleading expressions, ambiguous descriptions, and AI-generated writing patterns. Focuses on creating content that is read and understood, rather than just comprehensive.

**Best for**: Blog posts, technical articles, documentation, tutorials, README files, release notes.

### Installation

#### 1. Clone the Repository

```bash
git clone https://github.com/IsodaZen/custom-prompts.git
cd custom-prompts
```

#### 2. Configure Claude Code

**Project-level installation** (recommended for team sharing):

```bash
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
        ├── review:security.md
        ├── review:after.md
        ├── review:perf.md
        ├── review:prompt.md
        └── review:note.md
```

**Note**: Japanese versions (`commands-ja/`) are available in the repository for reference but are not required for installation.

## File Structure

```
custom-prompts/
├── README.md              # This file (English)
├── README.ja.md          # Japanese version
├── CLAUDE.md             # Detailed guide for Claude Code
├── CONTRIBUTING.md       # Contribution guidelines
├── commands/             # Command files (for installation)
│   ├── review:security.md
│   ├── review:after.md
│   ├── review:perf.md
│   ├── review:prompt.md
│   └── review:note.md
└── commands-ja/          # Japanese versions (for reference)
    ├── review:security.ja.md
    ├── review:after.ja.md
    ├── review:perf.ja.md
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
