# Claude Code 101 for Data Professionals

If you work with data — whether as a data engineer, analyst, or BI developer — you've probably heard about AI coding assistants. But most tutorials focus on web developers or general software engineering. What about us, the data folks?

This course will walk you through **Claude Code** — Anthropic's AI coding agent that runs right in your terminal. We'll start from the basics of how large language models work, go through installation and real-world examples, and finish with advanced topics like agent orchestration and extending Claude Code with external tools.

![Claude Code](img/Claude.png)

> Official documentation: [code.claude.com/docs](https://code.claude.com/docs/en/overview)

## Who is this for

- Data engineers who want to speed up their pipeline development
- Data analysts who want to automate repetitive tasks
- BI developers looking to leverage AI for faster delivery
- Anyone curious about trying Claude Code in practice

## Course Structure

### [Module 01: LLM Fundamentals](01-llm-fundamentals.md)

What are large language models and how do they actually work? We cover the basics — tokens, parameters, context windows, temperature — and introduce Anthropic's Claude model family. No prior AI knowledge needed.

### [Module 02: What is Claude Code](02-what-is-claude-code.md)

Install Claude Code, set up authentication, and learn the core slash commands. We compare Claude Code to other AI tools (Copilot, Cursor, ChatGPT) and show how to run it alongside your IDE for the best workflow.

### [Module 03: Claude Code Capabilities](03-claude-code-capabilities.md)

Deep dive into the agentic loop — how Claude Code reads files, edits code, runs commands, and verifies results in an autonomous cycle. Plus: multimodal input (screenshots, mockups, diagrams), headless mode for automation, and session management.

### [Module 04: Hands-on — Stock Analysis](04-stock-analysis.md)

Your first real project with Claude Code. We build a Python program that downloads Microsoft stock data, plots price charts with moving averages, adds trading volume, and annotates key events — all guided by Claude Code step by step.

### [Module 05: More Examples](05-more-examples.md)

Nine practical examples showing the breadth of what Claude Code can do: web scraper, file organizer, REST API with FastAPI, sales dashboard, Telegram bot, code refactoring, database utilities, pytest test suite, and auto-generated documentation.

### [Module 06: AI Code Quality](06-code-quality.md)

Not all AI-generated code is production-ready. We compare Claude models on speed, quality, and price, walk through a quality checklist (basic to advanced), review SWE-bench results, and discuss when you should never trust AI output.

### [Module 07: Agent Army — Autonomous Development](07-agent-army.md)

The most advanced module. Run multiple Claude Code agents in parallel — from simple multi-terminal setups to bash orchestrators, headless pipelines, specification-as-contract, and the built-in Agent Teams feature. Includes git worktrees, tmux setup, orchestration tools (Gas Town, OpenSpec, Claude MPM, cmux), and real-world patterns.

### [Module 08: MCP & Extensions](08-mcp-and-skills.md)

Extend Claude Code with the Model Context Protocol (MCP) to connect to GitHub, Sentry, PostgreSQL, Notion, Slack, and hundreds more. Create custom Agent Skills for your team's workflows. Set up Hooks for deterministic automation and explore Plugins.

### [Module 09: Best Practices](09-best-practices.md)

Battle-tested advice for getting the most out of Claude Code. The #1 rule (context management), the explore-plan-code workflow, writing specific prompts, session management (Esc, /rewind, /clear), CLAUDE.md best practices, and scaling patterns (writer/reviewer, fan-out).

### [Module 10: Other Models](10-other-models.md)

Use Claude Code with models beyond Claude — OpenRouter (400+ models, including free ones), Claude Code Router (smart task routing), Ollama (local models for privacy), LiteLLM, and official cloud providers (Bedrock, Vertex, Foundry). Plus: Cline and Kilo Code as VS Code alternatives when Claude Code isn't available.

## What You'll Need

- A computer with macOS, Linux, or Windows
- A terminal (command line)
- An Anthropic account with API key (or a [Claude Pro/Max](https://claude.ai/pricing) subscription)
- Python 3.10+ (for hands-on examples)

## Additional Courses and Resources

If you want to deepen your knowledge of Claude Code, here's a selection of courses:

| Course | Author | Duration | Cost |
| ------ | ------ | -------- | ---- |
| [Claude Code: A Highly Agentic Coding Assistant](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant/) | DeepLearning.AI + Anthropic | ~2 hours | Free |
| [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action) | Anthropic (official) | Self-paced | Free |
| [Claude Code: Software Engineering with Generative AI Agents](https://www.coursera.org/learn/claude-code) | Coursera / Vanderbilt University | ~5 hours | Free (audit) |
| [Claude Code for Everyone](https://ccforeveryone.com/) | Community | Self-paced | Free |

Also recommended from Anthropic:

- [Claude 101](https://anthropic.skilljar.com/) — Claude basics for everyday tasks
- [Building with the Claude API](https://anthropic.skilljar.com/) — building applications via API
- [Introduction to Model Context Protocol](https://anthropic.skilljar.com/) — building MCP servers in Python

---

*This course was created in 2025. Claude Code is actively developing, so some details may change. Always check the [official documentation](https://code.claude.com/docs/en/overview).*

*Connect with me on [LinkedIn](https://www.linkedin.com/in/dmitryanoshin/) or check out more content at [surfalytics.com](https://surfalytics.com).*
