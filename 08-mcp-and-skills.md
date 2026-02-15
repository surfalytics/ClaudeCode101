# Module 8: MCP and Agent Skills — Extending Claude Code

## Introduction

Claude Code on its own can do a lot: read files, write code, run commands. But its true power is unlocked when you **connect external tools**. In this module, we'll cover:

- **MCP (Model Context Protocol)** — a protocol for connecting Claude Code to external services: databases, GitHub, Jira, Sentry, and hundreds more
- **Agent Skills** — modular skills that make Claude a specialist in a specific domain

## Part 1: MCP — Model Context Protocol

**MCP** is an open standard for connecting AI to external tools. Simply put, MCP lets Claude Code work not just with files on your computer, but with any external service.

> Documentation: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

**What you can do with MCP:**

- **Work with task trackers**: "Implement the feature from Jira ticket ENG-4521 and create a PR on GitHub"
- **Analyze monitoring**: "Check Sentry — what errors appeared after the last deploy?"
- **Query databases**: "Find in PostgreSQL 10 users who registered yesterday"
- **Work with designs**: "Update the email template based on new mockups from Figma"
- **Automate routine**: "Create draft emails in Gmail for these 10 users"

**MCP Architecture:**

```
Claude Code
    ├── MCP Server: GitHub    → creating PRs, review, issues
    ├── MCP Server: Sentry    → error monitoring
    ├── MCP Server: PostgreSQL → database queries
    ├── MCP Server: Notion    → documentation
    └── MCP Server: Slack     → notifications
```

Each MCP server is a small program that "translates" Claude Code requests into commands for a specific service.

**MCP Server Types:**

| Type | How it works | When to use |
|------|-------------|-------------|
| **HTTP (remote)** | Connects to a cloud service via URL | GitHub, Sentry, Notion — cloud services |
| **SSE (remote)** | Legacy variant of HTTP | Older servers that haven't updated yet |
| **stdio (local)** | Runs as a process on your machine | Databases, local tools |

**Installing MCP servers:**

HTTP server (cloud):

```bash
# Connect GitHub
claude mcp add --transport http github https://api.githubcopilot.com/mcp/

# Connect Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Connect Sentry
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

Stdio server (local):

```bash
# Connect PostgreSQL
claude mcp add --transport stdio db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://user:pass@localhost:5432/mydb"
```

Server management:

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

**Popular MCP servers:**

| Server | What it does | Install command |
|--------|-------------|-----------------|
| **GitHub** | PRs, issues, code review | `claude mcp add --transport http github https://api.githubcopilot.com/mcp/` |
| **Sentry** | Error monitoring | `claude mcp add --transport http sentry https://mcp.sentry.dev/mcp` |
| **Notion** | Documentation, wiki | `claude mcp add --transport http notion https://mcp.notion.com/mcp` |
| **PostgreSQL** | Database queries | `claude mcp add --transport stdio db -- npx -y @bytebase/dbhub --dsn "..."` |
| **Slack** | Messages and channels | `claude mcp add --transport http slack https://mcp.slack.com/mcp` |

Full list: [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)

**Authentication:**

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

**Visibility Scopes:**

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

The `.mcp.json` file can be committed to git, and all team members will get access to the same servers.

**Practical example: Debugging with Sentry and GitHub**

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

**Practical example: Database queries**

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
```

## Part 2: Agent Skills — Modular Skills

**Agent Skills** are modular instruction packages that turn Claude from a universal assistant into a specialist in a specific area.

Imagine hiring a new employee. You give them a folder with instructions: "Here's how we work with databases. Here are templates. Here are scripts." Skills work the same way — it's an "instruction folder" for Claude.

**Why you need Skills:**

| Without Skills | With Skills |
|---------------|------------|
| You explain to Claude how to work with PDFs every time | Claude already knows — it has a PDF skill |
| You repeat the same instructions | Write instructions once — use forever |
| Claude gives generic answers | Claude gives answers following your rules and processes |

**Built-in Skills:**

| Skill | What it does |
|-------|-------------|
| **PowerPoint (pptx)** | Creating presentations, editing slides |
| **Excel (xlsx)** | Creating spreadsheets, data analysis, charts |
| **Word (docx)** | Creating and editing documents |
| **PDF (pdf)** | Generating PDF reports |

**Creating custom Skills for Claude Code:**

In Claude Code, skills are simply folders with a `SKILL.md` file.

```
.claude/skills/my-skill/
└── SKILL.md
```

Or in the home folder (for all projects):

```
~/.claude/skills/my-skill/
└── SKILL.md
```

**Example: Skill for database work**

```yaml
name: company-database
description: Working with the company database. Use when making
  database queries, creating reports, or analyzing user data.

# Working with Company Database

## Connection

Use SQLAlchemy with the DATABASE_URL environment variable.

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

**Example: Skill for code review**

```yaml
name: code-review
description: Conducts code review per company standards.
  Use for code review, PR checks, or quality analysis.

# Code Review per Company Standards

## Review Checklist

## Must check:
1. **Type hints** — all functions must have types
2. **Docstrings** — all public functions documented
3. **Tests** — tests exist for new code
4. **Security** — no SQL injection, XSS, hardcoded secrets
5. **Logging** — uses logging, not print

## Comment format:
- Critical: blocks merge
- Important: should be fixed
- Minor: can fix later
- Suggestion: optional, but improves code
```

## Part 3: MCP + Skills + Agent Teams = Superpower

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

**Example of a workday with MCP + Skills:**

Morning — check errors:
```
> Check Sentry for overnight errors. Any new ones?
  (MCP: Sentry → finds errors)

> Fix the critical error and create a PR
  (MCP: GitHub → creates PR with fix)
```

Day — work on a feature:
```
> What tasks are assigned to me in Jira?
  (MCP: Jira → shows tasks)

> Implement task ENG-4521.
  Check the database schema before starting.
  (MCP: PostgreSQL → shows schema)
  (Skill: code-review → checks code per standards)
```

Evening — deploy:
```
> Everything is ready. Deploy to staging and check logs.
  (Skill: deployment → follows the deployment process)
  (MCP: Sentry → checks for new errors)
```

## Part 4: Hooks — Automation Without AI

> Documentation: [code.claude.com/docs/en/hooks](https://code.claude.com/docs/en/hooks-guide)

**Hooks** are regular scripts that run **automatically** on specific events in Claude Code. Unlike instructions in CLAUDE.md (which Claude might ignore), hooks are **guaranteed** to execute every time.

**When to use Hooks:**

- Run a linter after every file edit
- Block changes to certain directories
- Format code after edits
- Send a notification when Claude touches critical files
- Run a security check before commit

**Example: Auto-formatting after edits**

Configure in `.claude/settings.json`:

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

**Event types:**

| Event | When it triggers |
|-------|-----------------|
| `afterFileEdit` | After editing a file |
| `beforeCommand` | Before running a command |
| `afterCommand` | After running a command |
| `onSessionStart` | When a session starts |
| `onSessionEnd` | When a session ends |
| `onCompact` | When context is compacted |

The key difference from CLAUDE.md: hooks are deterministic. They don't use AI, they just run a script. It's a guarantee, not a recommendation.

## Part 5: Plugins — Extension Packages

> Documentation: [code.claude.com/docs/en/plugins](https://code.claude.com/docs/en/plugins)

**Plugins** are packages that bundle Skills, Hooks, sub-agents, and MCP servers into one installable unit. Think of them as browser extensions, but for Claude Code.

**How to install:**

```
/plugin    # Open marketplace and select a plugin
```

## Extension Map

| Extension | What it does | When loaded | Context load |
|-----------|-------------|-------------|-------------|
| **CLAUDE.md** | Permanent instructions | Every session start | Every request |
| **Skills** | Knowledge and workflows | On demand | Low until used |
| **MCP** | External services | Session start | Every request |
| **Sub-agents** | Isolated tasks | On launch | Isolated |
| **Agent Teams** | Agent coordination | On launch | Isolated |
| **Hooks** | Automatic scripts | On event | Zero |
| **Plugins** | Extension packages | On install | Depends on content |

## Module Summary

- **MCP** — open protocol for connecting to external services (GitHub, Sentry, PostgreSQL, Notion, Slack)
- Three server types: HTTP, SSE (legacy), stdio (local)
- Three visibility scopes: local, project, user
- **Agent Skills** — modular instruction packages with progressive loading
- Built-in skills: PowerPoint, Excel, Word, PDF
- Custom skills — a folder with a SKILL.md file
- **Hooks** — deterministic scripts, zero context load
- **Plugins** — extension packages (Skills + Hooks + sub-agents + MCP)
- MCP + Skills + Hooks + Agent Teams = full workflow automation

---

[← Previous: Agent Army](07-agent-army.md) | [Back to Course](README.md) | [Next: Best Practices →](09-best-practices.md)
