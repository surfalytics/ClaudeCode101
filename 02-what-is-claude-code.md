# Module 2: What is Claude Code

> Official documentation: [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) | [Quickstart](https://code.claude.com/docs/en/quickstart)

## Introduction

**Claude Code** is an AI coding assistant that works right in your terminal. Unlike a regular chat with Claude, Claude Code can:

- Read your project files
- Create and edit files
- Run terminal commands
- Work with git
- Search the internet
- Understand the context of your entire project

| Tool | Where it works | What it can do |
|------|---------------|----------------|
| ChatGPT / Claude.ai | Web browser | Answers questions, writes code in chat |
| GitHub Copilot | IDE (VS Code, JetBrains) | Code autocompletion in the editor |
| Cursor | IDE | AI code editor with chat |
| **Claude Code** | **Terminal** | **Full project access, command execution, file management** |

![Claude Code — welcome screen](img/Claude.png)

## Installing Claude Code

**macOS / Linux / WSL:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**Alternative methods:**
```bash
# macOS via Homebrew
brew install --cask claude-code

# Windows via WinGet
winget install Anthropic.ClaudeCode
```

## Authentication Methods

**Option 1: Anthropic API Key**
```bash
export ANTHROPIC_API_KEY="sk-ant-..."
claude
```

**Option 2: Claude Max subscription** — Claude Pro ($20/month), Claude Max ($100/month or $200/month).

## Core Commands

| Command | What it does |
|---------|-------------|
| `/help` | Show help |
| `/exit` | Exit Claude Code |
| `/clear` | Clear conversation history |
| `/compact` | Compress context |
| `/model` | Show or switch model |
| `/cost` | Show current session cost |
| `/permissions` | Manage permissions |

## Operating Modes

```bash
claude                    # Normal interactive mode
claude -p "..."           # Execute one command and exit (headless)
claude --continue         # Continue previous conversation
claude --model claude-sonnet-4-20250514  # Launch with a specific model
```

## Permissions

| Action | Permission needed? |
|--------|-------------------|
| Reading files | No |
| Creating/editing files | Yes (first time) |
| Running commands (python, npm) | Yes (first time) |
| Running potentially dangerous commands (rm, sudo) | Yes (every time) |

The `--dangerously-skip-permissions` flag disables all permission prompts. Use with caution.

## Working with an IDE

The simplest approach for beginners is to run Claude Code directly in VS Code's built-in terminal. You see your project files, real-time changes, and Claude Code — all in one window:

![Claude Code in VS Code's built-in terminal](img/claude_in_vscode.png)

You can also use a separate terminal alongside your IDE:

```
┌─────────────────────────────┬──────────────────────────────┐
│   VS Code / IDE             │   Terminal                   │
│   📁 my-project/            │   $ claude                   │
│   ├── src/                  │                              │
│   │   ├── main.py      ◀───│── > Add logging              │
│   │   └── utils.py         │     to main.py               │
│   ├── tests/                │                              │
│   └── README.md             │   Claude Code creates files, │
│                             │   and you see changes        │
│   Syntax highlighting       │   in the IDE in real time    │
│   Git diff                  │                              │
└─────────────────────────────┴──────────────────────────────┘
```

## The CLAUDE.md File

Create a `CLAUDE.md` file in your project root to give Claude Code instructions about your project. Claude Code will automatically read this file and follow your rules.

## Module Summary

- Claude Code works in the terminal and has full access to your project
- Installation: `curl -fsSL https://claude.ai/install.sh | bash`
- Use slash commands for control
- Open your project in an IDE alongside the terminal for convenience

---

[← Previous: LLM Fundamentals](01-llm-fundamentals.md) | [Back to Course](README.md) | [Next: Claude Code Capabilities →](03-claude-code-capabilities.md)
