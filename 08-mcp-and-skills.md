# Module 8: MCP and Agent Skills — Extending Claude Code

## Introduction

Claude Code on its own can do a lot: read files, write code, run commands. But its true power is unlocked when you **connect external tools**. In this module, we'll cover two key technologies:

- **MCP (Model Context Protocol)** — a protocol for connecting Claude Code to external services: databases, GitHub, Jira, Sentry, and hundreds more
- **Agent Skills** — modular skills that make Claude a specialist in a specific domain

## Part 1: MCP — Model Context Protocol

### What is MCP

**MCP** is an open standard for connecting AI to external tools. Simply put, MCP lets Claude Code work not just with files on your computer, but with any external service.

Think of Claude Code as a craftsman, and MCP servers as his tools. Without tools, he can only work with his hands. With tools, he can do much more.

> Documentation: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

### What You Can Do with MCP

With connected MCP servers, Claude Code can:

- **Work with task trackers**: "Implement the feature from Jira ticket ENG-4521 and create a PR on GitHub"
- **Analyze monitoring**: "Check Sentry — what errors appeared after the last deploy?"
- **Query databases**: "Find in PostgreSQL 10 users who registered yesterday"
- **Work with designs**: "Update the email template based on new mockups from Figma"
- **Automate routine**: "Create draft emails in Gmail for these 10 users"

### MCP Architecture

```
Claude Code
    │
    ├── MCP Server: GitHub    → creating PRs, review, issues
    ├── MCP Server: Sentry    → error monitoring
    ├── MCP Server: PostgreSQL → database queries
    ├── MCP Server: Notion    → documentation
    └── MCP Server: Slack     → notifications
```

Each MCP server is a small program that "translates" Claude Code requests into commands for a specific service.

### MCP Server Types

| Type | How it works | When to use |
|------|-------------|-------------|
| **HTTP (remote)** | Connects to a cloud service via URL | GitHub, Sentry, Notion — cloud services |
| **SSE (remote)** | Legacy variant of HTTP | Older servers that haven't updated yet |
| **stdio (local)** | Runs as a process on your machine | Databases, local tools |

### Installing MCP Servers

#### HTTP Server (cloud)

Most popular services provide ready-made HTTP MCP servers:

```bash
# Connect GitHub
claude mcp add --transport http github https://api.githubcopilot.com/mcp/

# Connect Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Connect Sentry
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

#### Stdio Server (local)

Local servers run on your machine:

```bash
# Connect PostgreSQL
claude mcp add --transport stdio db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://user:pass@localhost:5432/mydb"

# Connect Airtable
claude mcp add --transport stdio --env AIRTABLE_API_KEY=YOUR_KEY airtable \
  -- npx -y airtable-mcp-server
```

#### Server Management

```bash
# View all connected servers
claude mcp list

# Details about a specific server
claude mcp get github

# Remove a server
claude mcp remove github

# Inside Claude Code — server status
/mcp
```

### Popular MCP Servers

| Server | What it does | Install command |
|--------|-------------|-----------------|
| **GitHub** | PRs, issues, code review | `claude mcp add --transport http github https://api.githubcopilot.com/mcp/` |
| **Sentry** | Error monitoring | `claude mcp add --transport http sentry https://mcp.sentry.dev/mcp` |
| **Notion** | Documentation, wiki | `claude mcp add --transport http notion https://mcp.notion.com/mcp` |
| **PostgreSQL** | Database queries | `claude mcp add --transport stdio db -- npx -y @bytebase/dbhub --dsn "..."` |
| **Slack** | Messages and channels | `claude mcp add --transport http slack https://mcp.slack.com/mcp` |

Full list: [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)

### Authentication

Many cloud MCP servers require authentication. Claude Code supports OAuth 2.0:

```bash
# 1. Add the server
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# 2. Inside Claude Code — authenticate
> /mcp
# Select "Authenticate" for the needed server
# A browser will open for login
```

For servers with API keys:

```bash
# Via authorization header
claude mcp add --transport http my-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

### Visibility Scopes

MCP servers can be configured at different levels:

| Scope | Where stored | Who sees it | When to use |
|-------|-------------|-------------|-------------|
| **local** (default) | `~/.claude.json` | Only you, only this project | Personal servers, secrets |
| **project** | `.mcp.json` in the project | Whole team (via git) | Shared team tools |
| **user** | `~/.claude.json` | Only you, all projects | Personal utilities for all projects |

```bash
# Add a server for the whole team (project scope)
claude mcp add --transport http paypal --scope project https://mcp.paypal.com/mcp
```

The `.mcp.json` file can be committed to git, and all team members will get access to the same servers:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "db": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub", "--dsn", "${DATABASE_URL}"],
      "env": {}
    }
  }
}
```

Note the `${DATABASE_URL}` — Claude Code supports environment variables in configuration. Each developer can set their own values.

### Practical Example: Debugging with Sentry and GitHub

Let's say you received a notification about a production error:

```
> Connect to Sentry. What new errors appeared in the last 24 hours?

Claude Code (via MCP):
"Found 3 new errors:
1. TypeError in billing/process.py line 45 — 127 occurrences
2. ConnectionError in api/external.py line 89 — 34 occurrences
3. ValidationError in auth/login.py line 23 — 12 occurrences"

> Show the stack trace for the first error and fix it

Claude Code:
1. Gets the stack trace from Sentry
2. Opens billing/process.py
3. Finds and fixes the bug
4. Runs tests

> Create a PR on GitHub with the fix

Claude Code (via MCP):
1. Creates a branch fix/billing-typeerror
2. Commits the fix
3. Creates a PR with bug description and Sentry link
```

The entire cycle — from error discovery to PR creation — takes just a few minutes.

### Practical Example: Database Queries

```
> How many new users registered in the last week?
  Break it down by day. Also show what countries they're from.

Claude Code (via MCP to PostgreSQL):
"New registrations for the last week:

| Date       | Count | Top-3 Countries     |
|------------|-------|---------------------|
| 2025-02-10 | 145   | US, DE, BR          |
| 2025-02-11 | 132   | US, UK, FR          |
| ...

Total: 987 new users. 12% growth compared to last week."

> Write a SQL query that finds our most active users
  and create a script for automated reporting

Claude Code:
1. Writes the SQL query
2. Creates a Python script with database connection
3. Adds CSV export and visualization
```

### MCP Resources via @-mentions

MCP servers can provide resources that you can reference via `@`:

```
> Analyze @github:issue://123 and suggest a solution

> Compare @postgres:schema://users with @docs:file://database/user-model
```

This works like regular @-mentions for files, but the data comes from external services.

### MCP Tool Search

When you have many MCP servers connected (dozens), all their tools can consume the context window. Claude Code solves this with **Tool Search**:

- Instead of loading all tools at once, Claude Code searches for the needed ones per request
- Activates automatically when tools occupy more than 10% of the context
- You won't notice the difference — everything works as before, just more efficiently

```bash
# Set the auto-activation threshold (5% of context)
ENABLE_TOOL_SEARCH=auto:5 claude

# Always enabled
ENABLE_TOOL_SEARCH=true claude

# Always disabled
ENABLE_TOOL_SEARCH=false claude
```

## Part 2: Agent Skills — Modular Skills

### What Are Agent Skills

**Agent Skills** are modular instruction packages that turn Claude from a universal assistant into a specialist in a specific area.

Imagine hiring a new employee. You give them a folder with instructions: "Here's how we work with databases. Here are templates. Here are scripts." Skills work the same way — it's an "instruction folder" for Claude.

> Documentation: [platform.claude.com/docs/en/agents-and-tools/agent-skills/overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Why You Need Skills

| Without Skills | With Skills |
|---------------|------------|
| You explain to Claude how to work with PDFs every time | Claude already knows — it has a PDF skill |
| You repeat the same instructions | Write instructions once — use forever |
| Claude gives generic answers | Claude gives answers following your rules and processes |

### Built-in Skills

Anthropic provides ready-made Skills for working with documents:

| Skill | What it does |
|-------|-------------|
| **PowerPoint (pptx)** | Creating presentations, editing slides |
| **Excel (xlsx)** | Creating spreadsheets, data analysis, charts |
| **Word (docx)** | Creating and editing documents |
| **PDF (pdf)** | Generating PDF reports |

These Skills work "out of the box" — on Claude.ai and through the API.

### How Skills Work: Three Loading Levels

Skills use the principle of **progressive loading** — Claude loads only what is needed right now.

#### Level 1: Metadata (always loaded)

At startup, Claude sees only a brief description of each skill:

```yaml
---
name: pdf-processing
description: Extracts text and tables from PDF files.
  Use when working with PDFs or when the user mentions PDF.
---
```

This costs only ~100 tokens per skill. You can install many skills without losing context.

#### Level 2: Instructions (loaded on demand)

When your task matches a skill's description, Claude reads the full instructions:

```markdown
# PDF Processing

## Quick Start

Use pdfplumber to extract text:

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

For filling forms, see FORMS.md.
```

This costs up to ~5000 tokens — loaded only when needed.

#### Level 3: Resources (loaded as needed)

Skills can contain additional files:

```
pdf-skill/
├── SKILL.md           # Main instructions
├── FORMS.md           # Guide to form filling
├── REFERENCE.md       # Detailed API documentation
└── scripts/
    └── fill_form.py   # Utility script
```

Claude loads these files only if they are needed for the current task. Scripts are executed via bash — their code doesn't enter the context, only the result.

| Level | When loaded | Cost | Content |
|-------|------------|------|---------|
| 1. Metadata | Always, at startup | ~100 tokens | Name and description |
| 2. Instructions | When skill is activated | Up to 5K tokens | SKILL.md |
| 3. Resources | As needed | Unlimited | Files, scripts, references |

### Creating Custom Skills for Claude Code

In Claude Code, skills are simply folders with a `SKILL.md` file.

#### Skill Structure

```
.claude/skills/my-skill/
└── SKILL.md
```

Or in the home folder (for all projects):

```
~/.claude/skills/my-skill/
└── SKILL.md
```

#### Example: Skill for Database Work

```yaml
---
name: company-database
description: Working with the company database. Use when making
  database queries, creating reports, or analyzing user data.
---

# Working with Company Database

## Connection

Use SQLAlchemy with the DATABASE_URL environment variable:

```python
from sqlalchemy import create_engine
import os

engine = create_engine(os.environ["DATABASE_URL"])
```

## Main Tables

| Table | Description | Key Fields |
|-------|------------|------------|
| users | Users | id, email, name, created_at |
| orders | Orders | id, user_id, total, status |
| products | Products | id, name, price, category |

## Rules

- NEVER do DELETE or UPDATE without WHERE
- Always use LIMIT for large tables
- For reports, use read-only connection
- Dates always in ISO 8601 format
```

#### Example: Skill for Code Review

```yaml
---
name: code-review
description: Conducts code review per company standards.
  Use for code review, PR checks, or quality analysis.
---

# Code Review per Company Standards

## Review Checklist

### Must check:
1. **Type hints** — all functions must have types
2. **Docstrings** — all public functions documented
3. **Tests** — tests exist for new code
4. **Security** — no SQL injection, XSS, hardcoded secrets
5. **Logging** — uses logging, not print

### Comment format:
- Critical: blocks merge
- Important: should be fixed
- Minor: can fix later
- Suggestion: optional, but improves code

## Output

Create a review.md file with the structure:
1. Brief description of changes
2. List of issues found (by category)
3. Overall assessment: approve / request changes
```

#### Example: Skill for Deployment

```yaml
---
name: deployment
description: Application deployment management. Use for deploying,
  setting up CI/CD, or working with Docker/Kubernetes.
---

# Application Deployment

## Environments

| Environment | URL | Branch |
|-------------|-----|--------|
| dev | dev.app.com | develop |
| staging | staging.app.com | release/* |
| production | app.com | main |

## Deployment Process

1. Make sure all tests pass
2. Create a version tag: `git tag v1.X.Y`
3. Run deploy: `make deploy ENV=staging`
4. Check logs: `make logs ENV=staging`
5. If OK — deploy to production

## Docker

```bash
# Build image
docker build -t app:latest .

# Run locally
docker compose up -d

# Logs
docker compose logs -f app
```

## Rollback

If there are problems in production:
```bash
make rollback ENV=production
```
```

### Where Skills Work

| Product | Built-in Skills | Custom Skills |
|---------|----------------|---------------|
| **Claude.ai** | Yes | Yes (upload via settings) |
| **Claude API** | Yes | Yes (via Skills API) |
| **Claude Code** | No | Yes (folders with SKILL.md) |

In Claude Code, skills are the file system. Simply create a folder and a file, and Claude will find them automatically.

### Skills Security

Skills are a powerful tool. Only use skills from trusted sources:

- Skills that you wrote yourself
- Skills from Anthropic (built-in)
- Skills from your team (via git)

Do not install skills from unknown sources — they may contain malicious instructions.

## Part 3: MCP + Skills + Agent Teams = Superpower

### Combining Everything Together

The most powerful scenarios combine MCP, Skills, and Agent Teams:

```
Project Specification
     │
     ├── Agent Team Lead (coordination)
     │    ├── MCP: GitHub (PRs, issues)
     │    ├── MCP: Jira (tasks)
     │    └── Skill: project-management
     │
     ├── Teammate: Backend Dev
     │    ├── MCP: PostgreSQL (DB queries)
     │    └── Skill: code-review
     │
     ├── Teammate: QA
     │    ├── MCP: Sentry (monitoring)
     │    └── Skill: testing-standards
     │
     └── Teammate: DevOps
          ├── MCP: AWS (infrastructure)
          └── Skill: deployment
```

### Example of a Workday with MCP + Skills

**Morning — check errors:**
```
> Check Sentry for overnight errors. Any new ones?
  (MCP: Sentry → finds errors)

> Fix the critical error and create a PR
  (MCP: GitHub → creates PR with fix)
```

**Day — work on a feature:**
```
> What tasks are assigned to me in Jira?
  (MCP: Jira → shows tasks)

> Implement task ENG-4521.
  Check the database schema before starting.
  (MCP: PostgreSQL → shows schema)
  (Skill: code-review → checks code per standards)
```

**Evening — deploy:**
```
> Everything is ready. Deploy to staging and check logs.
  (Skill: deployment → follows the deployment process)
  (MCP: Sentry → checks for new errors)
```

### Setting Up for a Team

Create `.mcp.json` in the project root and commit it to git:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "db": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub", "--dsn", "${DATABASE_URL}"],
      "env": {}
    }
  }
}
```

Add skills to the `.claude/skills/` folder in the project:

```
my-project/
├── .mcp.json                    # MCP servers for the team
├── .claude/
│   └── skills/
│       ├── code-review/
│       │   └── SKILL.md         # Review standards
│       ├── database/
│       │   └── SKILL.md         # Database work rules
│       └── deployment/
│           └── SKILL.md         # Deployment process
├── CLAUDE.md                    # General project rules
└── src/
    └── ...
```

Every team member will get the same tools and rules when launching Claude Code in the project.

## Part 4: Hooks — Automation Without AI

> Documentation: [code.claude.com/docs/en/hooks](https://code.claude.com/docs/en/hooks-guide)

### What Are Hooks

**Hooks** are regular scripts that run **automatically** on specific events in Claude Code. Unlike instructions in CLAUDE.md (which Claude might ignore), hooks are **guaranteed** to execute every time.

### When to Use Hooks

- Run a linter after every file edit
- Block changes to certain directories
- Format code after edits
- Send a notification when Claude touches critical files
- Run a security check before commit

### Example: Auto-formatting After Edits

Claude can write a hook itself. Just ask:

```
> Write a hook that runs eslint --fix after every file edit
```

Or configure manually in `.claude/settings.json`:

```json
{
  "hooks": {
    "afterFileEdit": [
      {
        "command": "npx eslint --fix $FILE",
        "description": "Auto-fix lint errors"
      }
    ]
  }
}
```

### Event Types

| Event | When it triggers |
|-------|-----------------|
| `afterFileEdit` | After editing a file |
| `beforeCommand` | Before running a command |
| `afterCommand` | After running a command |
| `onSessionStart` | When a session starts |
| `onSessionEnd` | When a session ends |
| `onCompact` | When context is compacted |

**The key difference from CLAUDE.md:** hooks are deterministic. They don't use AI, they just run a script. It's a guarantee, not a recommendation.

## Part 5: Plugins — Extension Packages

> Documentation: [code.claude.com/docs/en/plugins](https://code.claude.com/docs/en/plugins)

### What Are Plugins

**Plugins** are packages that bundle Skills, Hooks, sub-agents, and MCP servers into one installable unit. Think of them as browser extensions, but for Claude Code.

### Why You Need Them

- **Reusability** — one configuration for multiple projects
- **Distribution** — share your tools with the team
- **Marketplaces** — install ready-made extensions from the community

### How to Install

```
/plugin    # Open marketplace and select a plugin
```

### What's Inside a Plugin

```
my-plugin/
├── skills/
│   └── review/SKILL.md       # Review checklist
├── hooks/
│   └── settings.json          # Hooks for auto-formatting
├── agents/
│   └── security-reviewer.md   # Sub-agent for security checks
└── mcp/
    └── config.json            # MCP servers
```

Plugins with skills use namespaces: `/my-plugin:review` to avoid conflicts with others.

### Useful Plugins

If you work with typed languages (TypeScript, Go, Rust), install the **code intelligence** plugin — it gives Claude precise symbol navigation and automatic error detection after edits.

## Part 6: How Everything Works Together

> More details: [code.claude.com/docs/en/features-overview](https://code.claude.com/docs/en/features-overview)

### Extension Map

| Extension | What it does | When loaded | Context load |
|-----------|-------------|-------------|-------------|
| **CLAUDE.md** | Permanent instructions | Every session start | Every request |
| **Skills** | Knowledge and workflows | On demand | Low until used |
| **MCP** | External services | Session start | Every request |
| **Sub-agents** | Isolated tasks | On launch | Isolated |
| **Agent Teams** | Agent coordination | On launch | Isolated |
| **Hooks** | Automatic scripts | On event | Zero |
| **Plugins** | Extension packages | On install | Depends on content |

### Principle: Less Frequent is Better

Every extension takes up space in the context. Priority order:

1. **CLAUDE.md** — for rules that are needed ALWAYS (up to ~500 lines)
2. **Skills** — for reference materials and workflows (loaded on demand)
3. **MCP** — for connecting to external services (tool definitions — every request)
4. **Hooks** — for automation (zero context load)

### Extension Combinations

| Combination | How it works | Example |
|------------|-------------|---------|
| Skill + MCP | MCP provides the connection, Skill teaches how to use it | MCP connects the database, Skill describes the schema |
| Skill + sub-agent | Skill launches sub-agents for parallel work | `/review` launches 3 sub-agents: security, performance, style |
| CLAUDE.md + Skills | CLAUDE.md — rules always, Skills — references on demand | CLAUDE.md: "follow API conventions", Skill: full API guide |
| Hook + MCP | Hook triggers external actions via MCP | Hook sends to Slack when Claude touches critical files |

## Part 7: Custom Agents

> Documentation: [code.claude.com/docs/en/custom-agents](https://code.claude.com/docs/en/custom-agents)

### What Are Custom Agents

**Custom Agents** are reusable agent configurations stored in `.claude/agents/` directory. Think of them as "role profiles" — you define an agent's personality, tools, and constraints once, then invoke it by name.

While Skills give Claude *knowledge* (instructions and references), Custom Agents give Claude a *role* — a complete persona with specific behavior, permissions, and focus areas.

### Creating a Custom Agent

Create a markdown file in `.claude/agents/`:

```
.claude/agents/
├── security-reviewer.md
├── data-analyst.md
└── migration-helper.md
```

Each file defines the agent's behavior:

```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities
model: claude-opus-4-6
tools:
  - Read
  - Grep
  - Glob
  - Bash
allowed_tools_pattern: "^(Read|Grep|Glob|Bash)$"
---

# Security Reviewer

You are a security-focused code reviewer. Your job is to find
vulnerabilities in code.

## What to Check

1. SQL injection
2. XSS (Cross-Site Scripting)
3. Authentication/authorization flaws
4. Hardcoded secrets
5. Insecure dependencies
6. Input validation gaps

## Output Format

For each issue found:
- **Severity**: Critical / High / Medium / Low
- **File**: path and line number
- **Issue**: description
- **Fix**: recommended solution

## Rules

- NEVER modify files — only read and report
- Check EVERY file in the PR diff
- Always check for OWASP Top 10
```

### Invoking Custom Agents

In Claude Code, use the `/agents` command or reference them directly:

```
> /agents security-reviewer Review the changes in the last 3 commits

> /agents data-analyst Analyze the sales data in data/sales.csv
```

Custom agents can also be launched as teammates in Agent Teams:

```
Launch a security-reviewer agent to audit the auth module.
Launch a data-analyst agent to generate the quarterly report.
```

### Example: Data Analyst Agent

```markdown
---
name: data-analyst
description: Analyzes data files and generates reports with visualizations
model: claude-sonnet-4-5-20250929
---

# Data Analyst

You are a data analyst. When given a dataset, you:

1. Load and inspect the data (shape, types, nulls)
2. Generate summary statistics
3. Identify patterns and anomalies
4. Create visualizations (matplotlib/seaborn)
5. Write a report in markdown

## Tools

- Use pandas for data manipulation
- Use matplotlib/seaborn for charts
- Save charts to `reports/charts/`
- Save reports to `reports/`

## Rules

- Always show your methodology
- Flag data quality issues
- Include both summary and detailed findings
```

### Example: Migration Helper Agent

```markdown
---
name: migration-helper
description: Helps migrate code between frameworks or language versions
model: claude-opus-4-6
---

# Migration Helper

You specialize in code migrations. Your approach:

1. **Audit** — scan the codebase for patterns that need migration
2. **Plan** — create a migration plan with priorities
3. **Execute** — migrate file by file, running tests after each change
4. **Verify** — run the full test suite and check for regressions

## Rules

- Never migrate and change logic at the same time
- One migration step per commit
- Always keep the code in a working state
- If tests break, fix before continuing
```

### Custom Agents vs Skills vs CLAUDE.md

| Feature | CLAUDE.md | Skills | Custom Agents |
|---------|-----------|--------|---------------|
| **Purpose** | Project rules (always active) | Knowledge on demand | Complete role profiles |
| **When loaded** | Every session | When triggered | When invoked |
| **Scope** | Entire project | Specific domain | Specific task type |
| **Can restrict tools** | No | No | Yes |
| **Can set model** | No | No | Yes |
| **Best for** | Coding standards, project context | Reference docs, workflows | Specialized roles, reviewers |

### Team Setup with Custom Agents

Custom agents shine when shared across a team. Commit the `.claude/agents/` directory to git:

```
my-project/
├── .claude/
│   ├── agents/
│   │   ├── security-reviewer.md
│   │   ├── perf-tester.md
│   │   └── db-migration.md
│   └── skills/
│       └── ...
├── CLAUDE.md
└── src/
```

Every team member gets the same set of specialized agents, ensuring consistent quality and processes.

## Module Summary

### MCP
- MCP — open protocol for connecting Claude Code to external services
- Three server types: HTTP (cloud), SSE (legacy), stdio (local)
- Installation via `claude mcp add`
- Three visibility scopes: local, project, user
- Dozens of ready-made servers: GitHub, Sentry, PostgreSQL, Notion, Slack, and more
- Tool Search automatically optimizes working with many servers

### Agent Skills
- Skills — modular instruction packages for Claude
- Three loading levels: metadata → instructions → resources
- Built-in skills: PowerPoint, Excel, Word, PDF
- Custom skills — a folder with a SKILL.md file
- Only use skills from trusted sources

### Hooks
- Deterministic scripts, triggered on events
- Guaranteed execution (unlike CLAUDE.md)
- Ideal for linting, formatting, security checks
- Zero context load

### Plugins
- Packages of Skills + Hooks + sub-agents + MCP
- Marketplace for sharing extensions
- Namespaces to avoid conflicts

### Custom Agents
- Reusable role profiles stored in `.claude/agents/`
- Define behavior, tools, and constraints in a markdown file
- Invoke by name: `/agents security-reviewer`
- Can set model and restrict available tools
- Commit to git for team-wide consistency

### Together
- MCP + Skills + Custom Agents + Hooks + Agent Teams = full workflow automation
- `.mcp.json` + `.claude/skills/` + `.claude/agents/` + `.claude/settings.json` in git = unified tools for the whole team
- From bug discovery to deploying the fix — all through Claude Code

---

[← Previous: Agent Army](07-agent-army.md) | [Back to Course](README.md) | [Next: Best Practices →](09-best-practices.md)
