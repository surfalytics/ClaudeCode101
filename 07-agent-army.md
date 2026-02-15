# Module 7: Agent Army — Autonomous Development

> Documentation: [Sub-agents](https://code.claude.com/docs/en/sub-agents) | [Agent Teams](https://code.claude.com/docs/en/agent-teams) | [Parallel sessions](https://code.claude.com/docs/en/common-workflows#run-parallel-claude-code-sessions-with-git-worktrees)

## Introduction

A single Claude Code instance is a powerful tool. But what if you run **multiple agents simultaneously**, each with its own task? What if you give them a project specification and let them decide how to build it?

In this module, we'll explore how to turn one AI assistant into an entire development team.

## The Idea: From One Agent to an Army

**Single agent:**

```
You → Claude Code → result
```

You give a task, the agent executes it, you verify. This works, but a single agent has limitations:
- One context — it may "forget" the beginning of a long conversation
- One task — while it's writing code, it can't simultaneously run tests
- Sequential work — tasks are executed one after another

**Agent army:**

```
Specification
    ├── Agent 1: Backend API
    ├── Agent 2: Frontend
    ├── Agent 3: Tests
    ├── Agent 4: Documentation
    └── Agent 5: DevOps
```

Each agent works **in parallel** and **autonomously** on its own part. Like a real development team where everyone knows their area of responsibility.

## Approach 1: Multiple Terminals with Claude Code

The simplest approach — open multiple terminals.

**Step 1: Prepare a specification**

Create a `SPEC.md` file in the project root:

```markdown
# Specification: Task Manager API

## Description
REST API for task management with authentication.

## Technologies
- Python 3.12, FastAPI, SQLAlchemy 2.0, PostgreSQL
- pytest for tests
- Docker for deployment

## Modules
1. **auth** — registration, login, JWT tokens
2. **tasks** — CRUD for tasks
3. **users** — user profiles
4. **notifications** — email notifications

## Structure
src/
├── api/          # Endpoints
├── models/       # SQLAlchemy models
├── schemas/      # Pydantic schemas
├── services/     # Business logic
├── tests/        # Tests
└── main.py       # Entry point
```

**Step 2: Launch agents in different terminals**

**Terminal 1 — Architect:**
```bash
cd my-project
claude

> Read SPEC.md. Create the project structure:
  all folders, __init__.py files, base configs,
  requirements.txt, Dockerfile, docker-compose.yml.
  DON'T write business logic — only the project skeleton.
```

**Terminal 2 — Backend developer (after the architect finishes):**
```bash
cd my-project
claude

> Read SPEC.md. Implement the auth module:
  - User model in models/user.py
  - Schemas in schemas/auth.py
  - Auth service in services/auth.py
  - Endpoints in api/auth.py
  - Use JWT via python-jose
```

**Terminal 3 — Test developer:**
```bash
cd my-project
claude

> Read SPEC.md and the files in src/.
  Write tests for all existing modules.
  Use pytest + pytest-asyncio.
  Mock the database with SQLite in-memory.
  Run tests and make sure they pass.
```

**Terminal 4 — Documentation:**
```bash
cd my-project
claude

> Read all code in src/.
  Create a complete README.md:
  - Project description
  - Installation and setup
  - API documentation with all endpoints
  - Example requests (curl)
  - How to run tests
  - Docker instructions
```

**Coordination:**

1. **Architect works first** — creates structure and interfaces
2. **Developers work in parallel** — each in their own module
3. **Tester works last** — when code is already written
4. **Documentation comes at the end** — when the API is stable

## Approach 2: Headless Mode with Orchestration

A more advanced approach — manage agents from a script.

```bash
#!/bin/bash
# orchestrator.sh — launches an army of agents

PROJECT_DIR="$(pwd)"
SPEC="$PROJECT_DIR/SPEC.md"

echo "Launching agent army..."
echo "Specification: $SPEC"

# Step 1: Architect creates structure
echo "Phase 1: Architecture"

claude -p "Read SPEC.md. Create the project structure: \
all folders, configs, requirements.txt, base files. \
Only the skeleton, no business logic."

# Step 2: Parallel module development
echo "Phase 2: Parallel development"

# Launch 3 agents in parallel
claude -p "Read SPEC.md. Implement the auth module: \
models, schemas, services, endpoints." &
PID1=$!

claude -p "Read SPEC.md. Implement the tasks module: \
models, schemas, services, endpoints." &
PID2=$!

claude -p "Read SPEC.md. Implement the users module: \
models, schemas, services, endpoints." &
PID3=$!

# Wait for all agents to finish
wait $PID1 $PID2 $PID3

# Step 3: Tests
echo "Phase 3: Testing"

claude -p "Read all code in src/. Write tests for all modules. \
Run tests. If anything fails — fix the code and tests."

# Step 4: Documentation
echo "Phase 4: Documentation"

claude -p "Read the entire project. Create a complete README.md \
with API documentation, examples, and instructions."

echo "Project ready!"
```

Run the orchestrator:

```bash
chmod +x orchestrator.sh
./orchestrator.sh
```

## Approach 3: Claude Code Sub-agents (Built-in Feature)

Claude Code in IDE tools (Cursor, VS Code) can launch **sub-agents** — child processes that work autonomously.

When you give Claude Code a complex task, it can:

1. Break the task into subtasks
2. Launch a sub-agent for each subtask
3. Collect results
4. Integrate everything together

## Approach 4: Specification as a Contract

The most powerful approach is when you write **only the specification**, and the agents decide how to implement it.

**Level 1: High-level (for prototypes)**

```markdown
# Task Manager

Web application for task management.

Users can:
- Register and log in
- Create tasks with title, description, and deadline
- Mark tasks as completed
- Filter tasks by status
- Receive deadline notifications

Technologies: Python, FastAPI, PostgreSQL, Docker.
```

This is enough for an agent (especially on the Opus model) to create a complete project. But results may differ from expectations.

**Level 2: Detailed (recommended)**

```markdown
# Task Manager API

## Data Models

## User
| Field | Type | Description |
|-------|------|------------|
| id | UUID | Unique identifier |
| email | string | Email (unique) |
| password_hash | string | Password hash |
| name | string | Username |
| created_at | datetime | Registration date |

## Task
| Field | Type | Description |
|-------|------|------------|
| id | UUID | Unique identifier |
| title | string | Title (max 200 chars) |
| description | text | Description (optional) |
| status | enum | pending / in_progress / done |
| priority | enum | low / medium / high |
| due_date | datetime | Deadline (optional) |
| user_id | UUID | Task owner |
| created_at | datetime | Creation date |
| updated_at | datetime | Update date |

## API Endpoints

## Authentication
- POST /auth/register — registration
- POST /auth/login — login (returns JWT)
- POST /auth/refresh — token refresh

## Tasks
- GET /tasks — list current user's tasks
  - Query params: status, priority, sort_by, page, per_page
- POST /tasks — create task
- GET /tasks/{id} — get task
- PUT /tasks/{id} — update task
- DELETE /tasks/{id} — delete task

## Business Rules
- Users can only see their own tasks
- Deleting an account deletes all their tasks
- Deadline cannot be in the past
- Task title is required
```

**Level 3: Contract (for production)**

```markdown
# Contract: Task Manager API v1.0

## Non-Functional Requirements
- API response time: <200ms (p95)
- Test coverage: >85%
- Zero downtime deployment
- Rate limiting: 100 req/min per user

## Security
- JWT with refresh token rotation
- Bcrypt for passwords (cost factor 12)
- CORS only for allowed domains
- Rate limiting on /auth/* endpoints

## Error Handling
- All errors in format: {"error": {"code": "...", "message": "..."}}
- HTTP 400 — invalid data
- HTTP 401 — not authenticated
- HTTP 403 — no access rights
- HTTP 404 — resource not found
- HTTP 429 — rate limit exceeded
- HTTP 500 — internal error (log to Sentry)

## Tests
- Unit tests for each service
- Integration tests for each endpoint
- E2E tests for main scenarios
- Load tests (locust) for critical paths
```

**Which specification level to use?**

| Situation | Level | Why |
|-----------|-------|-----|
| Prototype / hackathon | 1 (high-level) | Speed matters more than quality |
| Working project | 2 (detailed) | Balance of speed and predictability |
| Production | 3 (contract) | Quality guarantee needed |

## Approach 5: Agent Teams — Built-in Team Coordination

This is an **official Claude Code feature** for coordinating multiple agents. Unlike the manual approaches above, Agent Teams is a built-in mechanism where agents coordinate work themselves, exchange messages, and maintain a shared task list.

> This is an experimental feature. Docs: [code.claude.com/docs/en/agent-teams](https://code.claude.com/docs/en/agent-teams)

**How it works:**

| Component | Role |
|-----------|------|
| **Team Lead** | Main Claude Code session. Creates the team, assigns tasks, collects results |
| **Teammates** | Separate Claude Code instances. Each works on its own task |
| **Task List** | Shared task list. Agents pick tasks and mark completion themselves |
| **Mailbox** | Messaging system between agents. They can communicate directly with each other |

The key difference from sub-agents: in Agent Teams, agents **communicate with each other directly**, not just report to the main one.

```
Sub-agents:                    Agent Teams:

  Main                           Lead
  ├── Sub 1 → report             ├── Agent 1 ←→ Agent 2
  ├── Sub 2 → report             ├── Agent 2 ←→ Agent 3
  └── Sub 3 → report             └── Agent 3 ←→ Agent 1
                                 (+ shared task list)
```

**Sub-agents vs Agent Teams:**

| | Sub-agents | Agent Teams |
|---|---|---|
| **Context** | Own context, result returned to caller | Own, fully independent |
| **Communication** | Only report to main agent | Agents communicate directly with each other |
| **Coordination** | Main agent manages everything | Shared task list, self-organization |
| **Best for** | Point tasks where only the result matters | Complex work requiring discussion and collaboration |
| **Tokens** | Less: results are compressed | More: each agent is a separate Claude instance |

**Enabling Agent Teams:**

The feature is off by default. Enable via `settings.json`:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

Or via environment variable:

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

**Creating a team:**

Launch Claude Code and describe the task and needed team in natural language:

```
I'm designing a CLI tool for tracking TODO comments
in code. Create a team of agents to research the task
from different angles: one on UX, one on technical architecture,
one as a devil's advocate.
```

Claude Code will:
1. Create a team with a shared task list
2. Launch agents for each role
3. Let them work and discuss with each other
4. Collect results
5. Shut down the team

**Display modes:**

**In-process** — all agents in one terminal:
- Switch between agents: `Shift+Up` / `Shift+Down`
- View task list: `Ctrl+T`
- Works in any terminal

**Split panes** — each agent in its own panel:
- Requires `tmux` or `iTerm2`
- See output from all agents simultaneously
- Click into an agent's panel to chat with it directly

**Team management examples:**

Set the number of agents and model:
```
Create a team of 4 agents for parallel refactoring
of these modules. Use Sonnet for each agent.
```

Require plan approval before work:
```
Launch an architect agent for refactoring the auth module.
Require plan approval before it starts changing code.
```

**Delegate Mode:** Without this mode, the lead sometimes starts doing tasks themselves instead of delegating. Delegate Mode limits the lead to coordination only. Enable: `Shift+Tab` after creating the team.

**When Agent Teams work best:**

Good for:
- **Research and review** — multiple agents simultaneously study different aspects
- **New modules** — each agent owns its module, no interference
- **Debugging with competing hypotheses** — agents check different theories in parallel and debate each other
- **Cross-layer changes** — frontend, backend, and tests, each under a separate agent

Not suitable for:
- Sequential tasks (use a single session)
- Edits in a single file (will cause conflicts)
- Tasks with many dependencies (coordination overhead eats the benefits)

**Example: Parallel code review**

```
Create a team of agents for reviewing PR #142. Launch three reviewers:
- One focuses on security
- One checks performance
- One validates test coverage
Each should review and report their findings.
```

**Example: Debugging with competing hypotheses**

```
Users complain that the app disconnects after the first
message instead of keeping the connection.
Create a team of 5 agents to investigate different hypotheses.
Have them debate each other and try to disprove their colleagues'
theories, like a scientific debate. Write findings to findings.md.
```

## Patterns for Working with Agent Armies

**Pattern 1: Pipeline**

```
Architect → Developer → Tester → Reviewer → Deploy
```

Each agent works sequentially, passing the result to the next.

**Pattern 2: Parallel Specialists**

```
              ┌─→ Agent: Backend ──┐
Specification─┼─→ Agent: Frontend ─┼─→ Integration
              └─→ Agent: DevOps ───┘
```

Agents work in parallel on different parts of the system. **Important:** this pattern requires clear interfaces between modules — define them in the specification upfront.

**Pattern 3: Generate + Verify**

```
Generator Agent → Reviewer Agent → Fixer Agent
```

One agent writes code, another reviews it, a third fixes the found issues.

**Pattern 4: Specialized Roles**

Give each agent a specific role:

```
Agent "Product Manager":
   "Read SPEC.md, find contradictions and ambiguities.
    Suggest clarifications."

Agent "Architect":
   "Design architecture based on SPEC.md.
    Create diagrams and ADR (Architecture Decision Records)."

Agent "Developer":
   "Implement module X per specification and architecture decisions."

Agent "QA":
   "Write tests. Find bugs. Check edge cases."

Agent "Tech Writer":
   "Create documentation: README, API docs, deployment guide."

Agent "Security Engineer":
   "Conduct security review. Find vulnerabilities. Suggest fixes."
```

## Git Worktrees and tmux: Infrastructure for Parallel Work

When multiple agents work simultaneously on one repository, the main problem is **file conflicts**. Two agents can't edit the same file in the same working copy. The solution — **git worktrees**.

**What is a git worktree:**

Git worktree lets you create **multiple working directories** from one repository. Each directory is connected to its own branch, but they all share one `.git`.

```
my-project/                  # Main working copy (main branch)
my-project-auth/             # Worktree for agent 1 (feature/auth branch)
my-project-api/              # Worktree for agent 2 (feature/api branch)
my-project-tests/            # Worktree for agent 3 (feature/tests branch)
```

**Setting up worktrees for agents:**

```bash
# Create worktree for the first agent
git worktree add -b feature/auth ../my-project-auth main

# Create worktree for the second agent
git worktree add -b feature/api ../my-project-api main

# Create worktree for the third agent
git worktree add -b feature/tests ../my-project-tests main

# Check the list of all worktrees
git worktree list
```

**Merging results:**

```bash
cd my-project

# Merge the auth branch
git merge feature/auth

# Merge the API branch
git merge feature/api

# Merge tests
git merge feature/tests

# If there are conflicts — ask Claude Code to resolve
claude
> Resolve all merge conflicts. Code from both branches is important.
```

**Cleanup:**

```bash
# Remove worktrees after merge
git worktree remove ../my-project-auth
git worktree remove ../my-project-api
git worktree remove ../my-project-tests

# Delete merged branches
git branch -d feature/auth feature/api feature/tests
```

**tmux: Managing multiple terminals**

When you have 5-10 agents running simultaneously, opening 10 terminal windows is inconvenient. **tmux** solves this: all terminals in one window.

tmux (Terminal MUltipleXer) lets you create **multiple terminals in one window**. You can split the screen into panels, switch between them, and see everything at once.

**Installing tmux:**

```bash
# macOS
brew install tmux

# Ubuntu/Debian
sudo apt install tmux

# Launch
tmux
```

**Basic tmux commands:**

All tmux commands start with a **prefix** — by default `Ctrl+b`.

| Action | Command |
|--------|---------|
| New panel to the right | `Ctrl+b %` |
| New panel below | `Ctrl+b "` |
| Switch panels | `Ctrl+b arrow keys` |
| New window (tab) | `Ctrl+b c` |
| Next window | `Ctrl+b n` |
| Previous window | `Ctrl+b p` |
| Close panel | `Ctrl+d` or `exit` |
| Detach from session | `Ctrl+b d` |
| Reattach to session | `tmux attach` |

**Example: 4 agents in tmux**

```
┌───────────────────┬───────────────────┐
│                   │                   │
│  Agent 1: Auth    │  Agent 2: API     │
│  (worktree auth)  │  (worktree api)   │
│                   │                   │
├───────────────────┼───────────────────┤
│                   │                   │
│  Agent 3: Tests   │  Agent 4: Docs    │
│  (worktree tests) │  (worktree docs)  │
│                   │                   │
└───────────────────┴───────────────────┘
```

**Worktrees + tmux + Agent Teams:**

All three tools work great together:

- **Worktrees** — each agent in its own isolated copy, no file conflicts
- **tmux** — all agents visible on one screen, fast switching
- **Agent Teams** — agents communicate with each other and coordinate work

## Orchestration Tools

Beyond Claude Code's built-in features, the community has created powerful tools for managing agent armies.

**Gas Town — Industrial Agent Factory**

[Gas Town](https://github.com/steveyegge/gastown) — the most ambitious orchestrator by Steve Yegge (ex-Amazon, ex-Google). His idea: Claude Code is not a tool, it's a building block. Real productivity begins when you run 20-30 agents simultaneously.

Gas Town uses specialized roles like a real team:

| Role | What it does |
|------|-------------|
| **Mayor** | Your main contact. Coordinates work, distributes tasks |
| **Polecats** (Workers) | Launched on demand, do specific tasks, create Merge Requests |
| **Refinery** | Solves the merge problem — combines changes from dozens of workers into main |
| **Witness** | Monitors workers, helps stuck agents |
| **Deacon** | Daemon orchestrator, pings everyone and maintains the system |

Gas Town is a tool for advanced users. It's expensive (tens of dollars per hour on API), complex, and experimental. But it shows where the industry is heading.

**OpenSpec — Specification-Driven Development**

[OpenSpec](https://openspec.dev/) — a lightweight framework where **the specification is primary**, and code is the consequence. Works with Claude Code, Cursor, Windsurf, Copilot, and Gemini CLI.

**Tool comparison:**

| Tool | Complexity | Scale | Key Feature |
|------|-----------|-------|-------------|
| **Agent Teams** (built-in) | Medium | 3-5 agents | Inter-agent communication, shared task list |
| **Gas Town** | Very high | 20-30 agents | Industrial factory with roles and workflows |
| **OpenSpec** | Low | Any | Specification as single source of truth |
| **Claude MPM** | Medium | 5-15 agents | 47+ ready-made roles and 44+ skills |
| **cmux** | Low | 3-5 agents | Isolated VS Code workspaces |

## Pitfalls and Limitations

1. **Parallel work conflicts** — two agents editing the same file. **Solution:** use git worktrees.
2. **Inconsistent interfaces** — one agent creates `get_user(id)`, another calls `fetch_user(user_id)`. **Solution:** define interfaces upfront in the spec.
3. **Code duplication** — different agents write the same utility. **Solution:** architect creates shared modules first.
4. **Context limitations** — each agent has its own context and doesn't know what others are doing. **Solution:** use shared files (SPEC.md, CLAUDE.md).
5. **Cost** — many agents = many API calls = more money. **Solution:** use Haiku for simple tasks, Opus only for architectural decisions.

## The Future: Fully Autonomous Agents

**Today:** the agent executes one task per your prompt. You coordinate work between agents. You verify the result.

**Tomorrow (already emerging):** the agent breaks tasks into subtasks itself, runs tests and fixes errors itself, asks for clarifications itself.

**Day after tomorrow:** you write only business requirements, an army of agents designs, codes, tests, and deploys. You review and approve the result.

The key skill of the future — **ability to write good specifications** and **ability to evaluate results**. This is already more important than the ability to write code by hand.

## Module Summary

- Agent army means running multiple Claude Code instances for parallel work
- 5 built-in approaches: manual terminals, bash orchestrator, IDE sub-agents, specification as contract, **Agent Teams**
- Agent Teams is a built-in feature with shared task lists and inter-agent messaging
- **Git worktrees** solve conflict problems: each agent in its own isolated copy
- **tmux** lets you see and manage dozens of agents on one screen
- External tools: Gas Town (industrial factory), OpenSpec (spec-first), Claude MPM (47+ roles), cmux
- Key to success — good specification and clear separation of responsibilities
- Patterns: pipeline, parallel specialists, generate + verify
- The future: fully autonomous agents that only need a specification

---

[← Previous: AI Code Quality](06-code-quality.md) | [Back to Course](README.md) | [Next: MCP & Extensions →](08-mcp-and-skills.md)
