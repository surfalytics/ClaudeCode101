# Module 3: Claude Code Capabilities

> Official documentation: [code.claude.com/docs/en/how-claude-code-works](https://code.claude.com/docs/en/how-claude-code-works)

## Overview

In this module, we'll explore all the core capabilities of Claude Code. You'll learn what it can do and how to use each feature as effectively as possible.

## How Claude Code Works: The Agentic Loop

When you give Claude Code a task, it works in three phases:

1. **Gather context** — reads files, searches the project
2. **Take action** — edits code, runs commands
3. **Verify results** — runs tests, checks output

These phases repeat in a loop until the task is solved:

![Claude Code agentic loop](img/agentic-loop.svg)

Claude decides which tool to use at each step. When you say "fix the failing tests," Claude might:

1. Run the tests — see what fails
2. Read the error messages
3. Find the relevant files in the project
4. Read the code to understand the problem
5. Edit the files
6. Run the tests again to verify the fix

You can interrupt at any moment (Esc) and redirect.

### Built-in Tools

| Category | What Claude Can Do |
|----------|-------------------|
| **Files** | Read, edit, create, rename |
| **Search** | Find files by name, search by content (regex) |
| **Execution** | Run commands, tests, servers, git |
| **Web** | Search the internet, open documentation |

## 1. Working with Files

### Reading and Analyzing Code

Claude Code can read any file in your project and explain it:

```
> Explain what the file src/main.py does

Claude Code will read the file and give a detailed explanation:
- What each function does
- Which libraries are used
- How different parts of the code relate to each other
```

### Creating Files

```
> Create a file utils/helpers.py with date utility functions:
  - parse_date(date_string) — parse a date from a string
  - format_date(date, format) — format a date
  - days_between(date1, date2) — count days between two dates
```

Claude Code will create the file with complete code, documentation, and type hints.

### Editing Files

```
> In main.py, add error handling to the load_data() function
```

Claude Code will precisely find the target function and add try/except blocks.

### Searching the Project

```
> Find all files where calculate_price() is used
```

```
> Find all TODO comments in the project
```

## 2. Running Commands

Claude Code can run any commands in the terminal:

### Running Programs

```
> Run main.py with the --verbose argument
```

```
> Run the tests
```

### Installing Dependencies

```
> Install the pandas, matplotlib, and yfinance libraries
```

Claude Code will execute:
```bash
pip install pandas matplotlib yfinance
```

### Working with Environments

```
> Create a Python virtual environment and install dependencies from requirements.txt
```

## 3. Working with Git

Claude Code works exceptionally well with git. This is one of its most useful features.

### Basic Operations

```
> Initialize a git repository
```

```
> Show git status
```

```
> Commit all changes with the message "added data analysis module"
```

### Smart Commits

```
> See what changed and make a commit with a good description
```

Claude Code will look at `git diff`, understand the essence of the changes, and write a meaningful commit message on its own.

### Working with Branches

```
> Create a new branch feature/data-analysis and switch to it
```

```
> Show the differences between the current branch and main
```

### Pull Requests

```
> Create a pull request describing all changes
```

Claude Code uses the `gh` CLI to create a PR with an automatic description.

## 4. Debugging and Fixing Errors

### Fixing Errors

```
> I have an error: "TypeError: cannot unpack non-iterable NoneType object"
  in processor.py at line 45. Fix it.
```

Claude Code will:
1. Read the file
2. Find the problematic location
3. Understand the cause of the error
4. Propose and apply a fix

### Analyzing Logs

```
> Look at the logs in app.log and tell me why the application crashed
```

### Running and Fixing Tests

```
> Run the tests. If anything fails — fix it.
```

Claude Code will run the tests, see the errors, analyze them, and fix the code.

## 5. Refactoring

### Improving Code

```
> Refactor the file data_processor.py:
  - Break large functions into smaller ones
  - Add type hints
  - Improve variable names
```

### Migration

```
> Rewrite this script from requests to httpx with async support
```

### Documentation

```
> Add docstrings to all functions in the utils/ module
```

## 6. Creating Projects from Scratch

Claude Code can create an entire project from your description:

```
> Create a REST API with FastAPI and a SQLite database:
  - User model (id, name, email, created_at)
  - CRUD operations for User
  - Tests with pytest
  - Dockerfile
  - README with launch instructions
```

Claude Code will create the entire project structure, all the files, and even run the tests to make sure everything works.

## 7. Working with Data

### Data Analysis

```
> Load data from sales.csv, show basic statistics,
  and plot charts of sales by month
```

### Working with APIs

```
> Write a script that fetches currency exchange rates from a public API
  and saves them to a CSV file
```

### Visualization

```
> Build a dashboard with charts from data.csv:
  - Line chart of sales by month
  - Pie chart by category
  - Correlation heatmap
```

## 8. Multi-file Tasks

Claude Code can work with many files simultaneously. This is one of its strengths.

### Example: Add logging to an entire project

```
> Add logging to all Python files in the src/ folder.
  Use the standard logging module.
  Log entry/exit of each function and all errors.
```

Claude Code will go through every file, understand the structure of each one, and add logging in the right places.

### Example: Rename a variable across the project

```
> Rename the variable 'usr' to 'user' in all project files
```

## 9. Headless Mode (Automation)

Claude Code can be used in scripts and CI/CD:

```bash
# Generate documentation
claude -p "Generate API documentation for all endpoints in src/api/"

# Code review
claude -p "Review the code in the latest commit and find potential issues"

# Auto-fix linter errors
claude -p "Fix all pylint errors in the project"
```

### Using in a CI/CD Pipeline

Anthropic provides an **official GitHub Action** — `anthropics/claude-code-action` — purpose-built for CI/CD integration. It handles authentication, manages permissions, and posts results directly as PR comments.

```yaml
# .github/workflows/code-review.yml
name: AI Code Review
on:
  pull_request:
  issue_comment:
    types: [created]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      issues: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: anthropics/claude-code-action@beta
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          direct_prompt: |
            Review this PR for:
            1. Code quality and best practices
            2. Potential bugs
            3. Security issues
            Provide actionable feedback.
```

The action supports multiple triggers:
- **On PR open** — automatic review of new pull requests
- **On comment** — mention `@claude` in a PR comment to ask questions or request changes
- **On push** — run checks on every push to a PR branch

You can also use headless mode directly for simpler setups:

```yaml
      - name: AI Review
        run: |
          claude -p "Review the changes in this PR and suggest improvements" \
            --output-format json
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

## 10. Extended Thinking

Claude Code can use "extended thinking" for complex tasks. This means the model spends more time analyzing before responding.

When this is useful:
- Complex architectural decisions
- Debugging tricky bugs
- Refactoring large pieces of code

Claude Code decides on its own when to use extended thinking, based on the complexity of the task.

## Multimodal Input (Multimodal Prompting)

Claude Code understands not just text, but also **images**. You can pass a screenshot, design mockup, or diagram — and Claude will work with it as context.

**How to pass an image:**

- **Drag & drop** — drag an image directly into the Claude Code terminal
- **Copy-paste** — copy an image and paste (`Ctrl+V` / `Cmd+V`)
- **File path** — specify a path: `@screenshot.png`

**Usage examples:**

```
> [paste a UI bug screenshot]
  See this bug? The "Save" button overlaps the text. Fix the CSS.

> [paste a Figma mockup]
  Implement this page design. Use Tailwind CSS.

> [paste an architecture diagram]
  Here's the microservices diagram. Create a project structure following this architecture.

> [paste a browser error screenshot]
  Here's what I see in the browser console. Find and fix this error.
```

**When this is useful:**

| Scenario | What you pass | What Claude does |
|----------|--------------|-----------------|
| UI bug | Page screenshot | Finds the CSS/HTML problem and fixes it |
| Design implementation | Figma/Sketch mockup | Writes the component code from the image |
| Architecture | System diagram | Creates project structure and modules |
| Browser error | Console screenshot | Finds the bug from the stack trace in the image |
| Code review | PR screenshot | Analyzes changes and gives feedback |

**Tip:** Multimodal input is especially powerful combined with result verification. Ask Claude: "Implement this mockup. Then take a screenshot of the result and compare with the original" — and it can iteratively improve the code until the result matches the mockup.

## Capabilities Table

| Capability | Example Use Case | Difficulty |
|-----------|-----------------|-----------|
| Reading files | "Explain this code" | Simple |
| Creating files | "Create a script for..." | Simple |
| Running commands | "Run the tests" | Simple |
| Git operations | "Commit with a description" | Simple |
| Debugging | "Fix this error" | Medium |
| Refactoring | "Improve the code structure" | Medium |
| Project creation | "Create a REST API" | Medium |
| Multimodal input | "Implement this mockup [screenshot]" | Medium |
| Multi-file tasks | "Add logging everywhere" | Complex |
| CI/CD integration | Automated code review | Complex |

## Working with Sessions

> Documentation: [code.claude.com/docs/en/how-claude-code-works](https://code.claude.com/docs/en/how-claude-code-works#work-with-sessions)

Claude Code saves conversations locally. You can continue, resume, and fork sessions:

![Session continuity in Claude Code](img/session-continuity.svg)

```bash
claude --continue          # Continue the last conversation
claude --resume            # Choose from recent sessions
claude --continue --fork-session  # Fork: new session with the old session's history
```

**Why this matters:**
- You started a task yesterday — continue today without losing context
- Want to try a different approach — `--fork-session` creates a branch from the current session
- Name sessions for easy access: `/rename oauth-migration`

**What gets loaded into the context window:**
- Your conversation (all messages, files, commands)
- Contents of CLAUDE.md
- MCP tool definitions
- Skill instructions (when invoked)

Monitor context size with `/context`. When it fills up — Claude automatically compresses the history, or use `/compact` manually.

## Limitations of Claude Code

It's important to know what Claude Code **cannot** do:

1. **Work with GUI applications** — it works only in the terminal
2. **Run Docker containers** (but it can create Dockerfiles)
3. **Connect to remote servers via SSH** (directly)
4. **Work indefinitely** — it has a context limit
5. **Guarantee code safety** — always review what it writes

## Tips for Effective Use

### 1. Provide Context

Bad:
```
> fix the bug
```

Good:
```
> In the function calculate_total() in billing.py, when the item list
  is empty, it returns None instead of 0. Fix this and add a test.
```

### 2. Break Down Large Tasks

Instead of:
```
> Create a complete task management application
```

Better:
```
> Step 1: Create a data model for tasks (Task) with fields:
  title, description, status, created_at, due_date
```

Then:
```
> Step 2: Add API endpoints for CRUD operations on tasks
```

### 3. Use /compact

If the conversation gets long and Claude Code starts "forgetting" context:
```
/compact
```

This command compresses the conversation history while preserving the most important information.

### 4. Always Verify Results

Always review changes before committing:
```
> Show git diff
```

## Module Summary

- Claude Code works through an agentic loop: gather context → take action → verify → repeat
- Claude Code is a powerful tool that can do almost everything a developer does in the terminal
- It works with files, git, commands, and can create entire projects
- Use specific prompts and break large tasks into steps
- Always review code that Claude Code generates
- Multimodal input — pass screenshots, mockups, and diagrams directly in your prompt
- Headless mode (`claude -p`) lets you use Claude Code in automation and CI/CD

---

[← Previous: What is Claude Code](02-what-is-claude-code.md) | [Back to Course](README.md) | [Next: Hands-on: Stock Analysis →](04-stock-analysis.md)
