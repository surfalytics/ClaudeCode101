# Module 2: What is Claude Code

> Official documentation: [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) | [Quickstart](https://code.claude.com/docs/en/quickstart)

## Introduction

**Claude Code** is an AI coding assistant that works right in your terminal. Unlike a regular chat with Claude (via the web interface), Claude Code can:

- Read your project files
- Create and edit files
- Run terminal commands
- Work with git
- Search the internet
- Understand the context of your entire project

Simply put, Claude Code is like an experienced developer sitting next to you and helping you write code. Except it lives in your terminal.

## How Does Claude Code Differ from Other AI Tools?

| Tool | Where it works | What it can do |
|------|---------------|----------------|
| ChatGPT / Claude.ai | Web browser | Answers questions, writes code in chat |
| GitHub Copilot | IDE (VS Code, JetBrains) | Code autocompletion in the editor |
| Cursor | IDE | AI code editor with chat |
| **Claude Code** | **Terminal** | **Full project access, command execution, file management** |

The key difference: Claude Code works in the **terminal** and has **full access to your project**. It can open files on its own, run tests, commit changes, and much more.

![Claude Code — welcome screen](img/Claude.png)

## Installing Claude Code

> Official documentation: [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview)

### Requirements

- **macOS**, **Linux**, or **Windows** (via WSL, or natively)
- An Anthropic account (API key or Claude Max subscription)

### Step 1: Install Claude Code

The simplest way — via the official installer:

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

The native installation (via `curl`) updates automatically. Homebrew and WinGet do not — you need to update manually.

### Step 2: Verify the Installation

```bash
claude --version
```

### Step 3: First Launch

Navigate to your project folder and run:

```bash
cd /path/to/your/project
claude
```

On the first launch, Claude Code will ask you to authenticate. Follow the on-screen instructions.

## Authentication Methods

### Option 1: Anthropic API Key

Suitable for developers who already use the Anthropic API.

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
claude
```

You pay per request according to Anthropic API pricing.

### Option 2: Claude Max Subscription

Suitable for those who want unlimited usage.

- Claude Pro subscription ($20/month) — limited usage
- Claude Max subscription ($100/month) — higher limits for Claude Code
- Claude Max subscription ($200/month) — maximum limits

On the first run, `claude` will open a browser for authentication via your account.

## Core Commands

### Launch and Exit

```bash
# Launch Claude Code in the current folder
claude

# Exit Claude Code
# Type: /exit
# Or press: Ctrl+C
```

### Slash Commands (/ commands)

Inside Claude Code, you can use special commands:

| Command | What it does |
|---------|-------------|
| `/help` | Show help |
| `/exit` | Exit Claude Code |
| `/clear` | Clear conversation history |
| `/compact` | Compress context (useful for long sessions) |
| `/model` | Show or switch model |
| `/cost` | Show current session cost |
| `/permissions` | Manage permissions |
| `/doctor` | Diagnose problems |

### Operating Modes

Claude Code can work in different modes:

```bash
# Normal interactive mode
claude

# Execute one command and exit (headless)
claude -p "explain what the file main.py does"

# Continue the previous conversation
claude --continue

# Launch with a specific model
claude --model claude-sonnet-4-5-20250929
```

## Your First Example: Hello World

Let's try creating a simple project with Claude Code.

### 1. Create a Project Folder

```bash
mkdir my-first-project
cd my-first-project
claude
```

### 2. Ask Claude Code to Create a Program

In interactive mode, type:

```
Create a file hello.py that prints "Hello, World!"
and the current date
```

Claude Code will:
1. Create the file `hello.py`
2. Write the code
3. Ask for permission to create the file
4. Create the file

### 3. Run It

```
Run hello.py
```

Claude Code will execute `python hello.py` and show the result.

## Permissions

Claude Code always asks for permission before dangerous actions. This is an important part of security.

### Permission Levels

| Action | Permission needed? |
|--------|-------------------|
| Reading files | No |
| Creating/editing files | Yes (first time) |
| Running commands (python, npm) | Yes (first time) |
| Running potentially dangerous commands (rm, sudo) | Yes (every time) |
| Git operations | Yes (first time) |

When Claude Code asks for permission, you'll see:

```
Claude wants to run: python hello.py
Allow? (y/n/always)
```

- **y** — allow once
- **n** — deny
- **always** — allow always for this type of command

### Skip Permissions Mode (dangerously-skip-permissions)

Constant permission prompts can be annoying, especially when you're working on a personal project and trust Claude Code. For such cases, there's a flag that disables all prompts:

```bash
claude --dangerously-skip-permissions
```

With this flag, Claude Code will:
- Create and edit files without asking
- Run any commands without confirmation
- Do git operations automatically
- Install dependencies without asking

**When this is convenient:**
- Personal projects and experiments
- Prototyping when you need maximum speed
- You understand well what Claude Code does and control the process
- Headless mode (`claude -p`) in automation scripts

**When you should NOT use it:**
- Work projects with important code
- If the folder contains files that shouldn't be accidentally deleted
- If you're just starting with Claude Code and want to understand what it does

The flag name speaks for itself: **dangerously**. Claude Code can execute any command — including `rm -rf`, if it decides it's needed. Use with caution.

An intermediate option — grant permissions via **always** for specific command types. You allow running `python` once, and Claude Code won't ask again. But for dangerous commands, permission will still be required.

## Working with an IDE: See Changes in Real Time

Claude Code works in the terminal, and that's its strength. But the terminal has a downside: you don't see how files change. Code flashes by in the output, but there's no familiar syntax highlighting, file navigation, or diff view.

The solution is simple: **open the project in an IDE alongside the terminal**.

### Recommended Setup

Open two things simultaneously:

1. **Terminal** with Claude Code — this is where you give commands
2. **IDE** with the same project — this is where you see the result

```
┌─────────────────────────────┬──────────────────────────────┐
│                             │                              │
│   VS Code / IDE             │   Terminal                   │
│                             │                              │
│   📁 my-project/            │   $ claude                   │
│   ├── src/                  │                              │
│   │   ├── main.py      ◀───│── > Add logging              │
│   │   └── utils.py         │     to main.py               │
│   ├── tests/                │                              │
│   └── README.md             │   Claude Code creates files, │
│                             │   and you see changes        │
│   Syntax highlighting       │   in the IDE in real time    │
│   Git diff                  │                              │
│   Code navigation           │                              │
│                             │                              │
└─────────────────────────────┴──────────────────────────────┘
```

### VS Code

The most popular option. Open the project:

```bash
# Open the current folder in VS Code
code .
```

VS Code automatically tracks file changes. When Claude Code creates or edits a file, you'll immediately see:
- A new file in the project tree
- Changed lines highlighted in the editor
- Git changes visible in the Source Control tab

![Claude Code in VS Code's built-in terminal](img/claude_in_vscode.png)

**Useful VS Code extensions:**
- **GitLens** — shows who changed each line and when
- **Error Lens** — shows errors directly in the code
- **Python** — highlighting, linting, autocompletion for Python

### Cursor

Cursor is a VS Code fork with built-in AI. You can use Claude Code in Cursor's terminal:

```
Cursor → Terminal → New Terminal → run claude
```

Advantage: everything in one window. Code on the left, terminal with Claude Code at the bottom.

### JetBrains (PyCharm, IntelliJ, WebStorm)

Works the same way: open the project in the IDE and run Claude Code in the terminal (built-in or separate).

```bash
# Open the project in PyCharm from the terminal
pycharm .
```

### Any Other Editor

Any editor that can automatically refresh files when they change on disk will work:
- **Sublime Text** — lightweight and fast
- **Vim / Neovim** — for terminal enthusiasts
- **Zed** — a new fast editor

### Tip: Built-in Terminal

Most IDEs have a built-in terminal. Use it — no need to switch between windows:

- **VS Code**: `Ctrl+`` ` (or `Cmd+`` ` on Mac)
- **Cursor**: `Ctrl+`` `
- **PyCharm**: `Alt+F12`

Run `claude` right in the built-in terminal, and you'll see file changes without switching windows.

## How Claude Code "Sees" Your Project

When you run `claude` in a project folder, it:

1. **Analyzes the structure** — looks at what files and folders exist
2. **Reads key files** — README, package.json, requirements.txt, etc.
3. **Understands the context** — knows what the project is and what language it uses
4. **Reads files on request** — opens needed files when you ask about them

Claude Code **does not send your entire project** to Anthropic servers. It only reads the files needed to answer your question.

## The CLAUDE.md File

You can create a `CLAUDE.md` file in the project root to give Claude Code instructions about your project:

```markdown
# Instructions for Claude Code

## About the Project
This is a Python/FastAPI web application for data analysis.

## Rules
- Use Python 3.11
- Write tests for each new function
- Use type hints
- Comments in English

## Structure
- src/ — source code
- tests/ — tests
- data/ — data
```

Claude Code will automatically read this file and follow your rules.

## Tips for Beginners

1. **Be specific** — the more precisely you describe the task, the better the result
2. **Review the code** — always look at what Claude Code created
3. **Use git** — Claude Code works great with git and can make commits
4. **Don't fear mistakes** — if something goes wrong, just say "undo the changes"
5. **Ask questions** — Claude Code can explain any code in the project

## Module Summary

- Claude Code works in the terminal and has full access to the project
- Installation: `curl -fsSL https://claude.ai/install.sh | bash` (or Homebrew / WinGet)
- Use slash commands (`/help`, `/clear`, `/compact`) for control
- Claude Code always asks for permission before making changes
- The `--dangerously-skip-permissions` flag disables all prompts (use with caution)
- Open the project in an IDE (VS Code, Cursor, PyCharm) alongside the terminal — so you see changes in real time
- The `CLAUDE.md` file helps configure Claude Code's behavior for your project

---

[← Previous: LLM Fundamentals](01-llm-fundamentals.md) | [Back to Course](README.md) | [Next: Claude Code Capabilities →](03-claude-code-capabilities.md)
