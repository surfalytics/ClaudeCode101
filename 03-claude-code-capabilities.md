# Module 3: Claude Code Capabilities

> Official documentation: [code.claude.com/docs/en/how-claude-code-works](https://code.claude.com/docs/en/how-claude-code-works)

## How Claude Code Works: The Agentic Loop

When you give Claude Code a task, it works in three phases:

1. **Gather context** — reads files, searches the project
2. **Take action** — edits code, runs commands
3. **Verify results** — runs tests, checks output

These phases repeat in a loop until the task is solved:

![Claude Code agentic loop](img/agentic-loop.svg)

Claude decides which tool to use at each step. You can interrupt at any moment (Esc) and redirect.

## Built-in Tools

| Category | What Claude Can Do |
|----------|-------------------|
| **Files** | Read, edit, create, rename |
| **Search** | Find files by name, search by content (regex) |
| **Execution** | Run commands, tests, servers, git |
| **Web** | Search the internet, open documentation |

## Working with Files

```
> Explain what the file src/main.py does
> Create a file utils/helpers.py with date utility functions
> In main.py, add error handling to the load_data() function
> Find all files where calculate_price() is used
```

## Running Commands

```
> Run main.py with the --verbose argument
> Install pandas, matplotlib, and yfinance libraries
> Create a Python virtual environment and install dependencies
```

## Working with Git

```
> Initialize a git repository
> See what changed and make a commit with a good description
> Create a pull request describing all changes
```

## Debugging and Fixing Errors

```
> I have an error: "TypeError: cannot unpack non-iterable NoneType object"
  in processor.py at line 45. Fix it.
> Run the tests. If anything fails — fix it.
```

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

## Headless Mode (Automation)

```bash
claude -p "Generate API documentation for all endpoints in src/api/"
claude -p "Review the code in the latest commit and find potential issues"
```

## Working with Sessions

![Session continuity in Claude Code](img/session-continuity.svg)

```bash
claude --continue          # Continue the last conversation
claude --resume            # Choose from recent sessions
claude --continue --fork-session  # Fork: new session with the old session's history
```

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

## Module Summary

- Claude Code works through an agentic loop: gather context → take action → verify → repeat
- It works with files, git, commands, and can create entire projects
- Multimodal input — pass screenshots, mockups, and diagrams directly in your prompt
- Headless mode (`claude -p`) lets you use Claude Code in automation

---

[← Previous: What is Claude Code](02-what-is-claude-code.md) | [Back to Course](README.md) | [Next: Hands-on: Stock Analysis →](04-stock-analysis.md)
