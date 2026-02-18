# Module 10: Claude Code with Other Models

> Links: [Claude Code Router](https://github.com/musistudio/claude-code-router) | [OpenRouter](https://openrouter.ai/) | [Official providers](https://code.claude.com/docs/en/third-party-integrations)

## Why Use Other Models

Claude Code works with Anthropic models (Claude) by default. But there are situations where you'd want to use other models:

- **Free models** — for simple tasks, you don't need to pay
- **Subscription limits** — ran out of tokens at 2 AM during debugging
- **Privacy** — code shouldn't leave your computer
- **Experiments** — try how GPT, Gemini, or Qwen handle the same tasks
- **Cost optimization** — simple tasks on a cheap model, complex ones on a powerful model

## How It Works: ANTHROPIC_BASE_URL

Claude Code uses an API to communicate with the model. The key trick: you can swap the URL where Claude Code sends requests:

```bash
export ANTHROPIC_BASE_URL="https://other-server.com/api"
export ANTHROPIC_AUTH_TOKEN="your-key"
export ANTHROPIC_API_KEY=""  # must be explicitly empty to prevent conflicts with old Anthropic key
```

Claude Code thinks it's talking to Anthropic, but requests actually go to another server. That server needs to support the Anthropic Messages API.

## Method 1: OpenRouter — Universal Gateway

[OpenRouter](https://openrouter.ai/) is a single API with access to 400+ models from 60+ providers. One registration, one key, hundreds of models.

```bash
export ANTHROPIC_BASE_URL="https://openrouter.ai/api"
export ANTHROPIC_AUTH_TOKEN="or_your_key"
export ANTHROPIC_API_KEY=""  # Important: leave empty to avoid conflicts

claude --model openai/gpt-oss-20b
```

> **Important:** if you previously logged into Claude Code via Anthropic, run `/logout` before using OpenRouter. Also, don't use a `.env` file — Claude Code doesn't read it. Add variables to `~/.bashrc` or `~/.zshrc` instead.

**Free accounts:** OpenRouter gives access to free models without depositing credits, but with a limit of ~50 API calls per day to free models. For active use, consider topping up your balance with at least $5.

Free models:

| Model | Description | Context |
|-------|------------|---------|
| `openai/gpt-oss-20b:free` | Open-weight model from OpenAI | 128K |
| `qwen/qwen3-coder:free` | Optimized for code | 262K |
| `deepseek/deepseek-r1:free` | Strong reasoning, good for debugging | 128K |

These models cost $0. For simple tasks (explaining code, formatting, generating documentation) they're sufficient.

Through OpenRouter, premium models are also available: GPT-4o, Gemini, Claude (sometimes with better availability than directly). Pay-as-you-go.

## Method 2: Claude Code Router — Smart Proxy

[Claude Code Router](https://github.com/musistudio/claude-code-router) is a proxy that sits between Claude Code and model providers. Its main feature is **smart routing**: different tasks are sent to different models.

```bash
npm install -g @musistudio/claude-code-router
```

Configuration `.claude-code-router/config.json`:

```json
{
  "LOG": true,
  "API_TIMEOUT_MS": 600000,
  "Providers": [
    {
      "name": "openrouter",
      "api_base_url": "https://openrouter.ai/api/v1/chat/completions",
      "api_key": "${OPENROUTER_API_KEY}",
      "models": ["qwen/qwen3-coder:free", "openai/gpt-oss-20b:free"]
    },
    {
      "name": "ollama",
      "api_base_url": "http://localhost:11434/v1/chat/completions",
      "api_key": "ollama",
      "models": ["qwen3:4b", "deepseek-coder:6.7b"]
    }
  ]
}
```

Launch:

```bash
# Start the router
ccr start

# Open the web UI for configuration
ccr ui
```

Smart routing:

| Category | Which model to use | Why |
|----------|-------------------|-----|
| **Default** | Qwen3 Coder (free) | Simple requests: explanations, formatting |
| **Think** | DeepSeek R1 | Complex reasoning, debugging |
| **Long Context** | Model with large context | Working with large codebases |
| **Background** | Cheap/fast model | Background tasks |

Result: "format this JSON" goes to a free model, while "design a microservice architecture" goes to Claude or GPT.

## Method 3: Ollama — Models on Your Own Computer (Free)

### What is Ollama

[Ollama](https://ollama.com/) is a free tool for running large language models (LLMs) locally on your computer. Think of it as "Docker for LLMs": you download a model with one command and start using it immediately. Ollama is built on [llama.cpp](https://github.com/ggml-org/llama.cpp) and optimized for running on regular computers — no expensive servers or cloud subscriptions needed.

**Key features:**

- **Completely free** — no token fees, subscriptions, or limits
- **Privacy** — code and data never leave your computer
- **Works offline** — no internet needed after downloading a model
- **Simple CLI** — `ollama pull`, `ollama run`, `ollama launch` — everything is intuitive
- **OpenAI-compatible API** — works with any tools, including Claude Code
- **GPU acceleration** — supports Apple Silicon (Metal), NVIDIA (CUDA), and AMD (ROCm)
- **Model library** — access hundreds of open-source models via [ollama.com/library](https://ollama.com/library)

Starting with **version 0.14.0**, Ollama supports the **Anthropic Messages API**, making the integration with Claude Code native — no proxy needed.

### Installation

```bash
# macOS (via Homebrew)
brew install ollama

# macOS / Linux (universal)
curl -fsSL https://ollama.com/install.sh | sh

# Or download the app from ollama.com
```

### Quick Start: ollama launch

The simplest way — the `ollama launch` command automatically configures environment variables and launches Claude Code:

```bash
# Download a model and launch Claude Code with one command
ollama launch claude

# Or configure first, then launch
ollama launch claude --config
```

The `ollama launch` command sets all required environment variables automatically — no manual configuration needed.

### Manual Setup

If you want full control over the process:

```bash
# Download models for code
ollama pull qwen3-coder
ollama pull gpt-oss:20b
ollama pull glm-4.7

# Connect to Claude Code
export ANTHROPIC_BASE_URL="http://localhost:11434"
export ANTHROPIC_AUTH_TOKEN="ollama"

# Launch Claude Code with your chosen model
claude --model qwen3-coder
```

### Recommended Models for Claude Code

| Model | Size | Best for | Min RAM |
|-------|------|----------|---------|
| `qwen3-coder` | ~30B | Best for code, long context (262K) | 32 GB |
| `gpt-oss:20b` | 20B | Open-weight from OpenAI, good balance | 16 GB |
| `glm-4.7` | 30B MoE (3B active) | Native tool calling, fast | 32 GB |
| `gpt-oss:120b` | 120B | Maximum quality, needs powerful hardware | 64+ GB |

> **Important:** Claude Code requires models with a large context window. At least **64K tokens** is recommended for comfortable coding.

### When This is Useful

- **Private code** — client data, internal company code, NDA projects
- **No internet** — working on a plane, in an isolated environment, on air-gapped servers
- **No limits** — run as much as you want, only limited by your hardware
- **No cost** — $0 forever, you only pay for electricity
- **Experiments** — try new open-source models right after release

### Hardware Requirements

| Model Size | RAM | GPU | Speed |
|-----------|-----|-----|-------|
| 4B parameters | 8 GB | Not required | Fast |
| 7–20B parameters | 16–32 GB | Recommended | Medium |
| 30B+ parameters | 32+ GB | Required | Slow without GPU |

> **Honest warning about speed:** local models are significantly slower than cloud APIs. On a MacBook Pro with M1 Max (64 GB RAM), a simple request can take 30–60 seconds, and file operations up to 2 minutes. For comfortable work, Apple Silicon with 32+ GB unified memory or a PC with an NVIDIA GPU is recommended.

## Method 4: LiteLLM — Proxy for Mixed Configurations

[LiteLLM](https://docs.litellm.ai/) is a proxy that lets you switch between Anthropic, OpenAI, Ollama, and dozens of other providers through a single API.

```yaml
# litellm_config.yaml
model_list:
  - model_name: gpt-oss-20b
    litellm_params:
      model: openai/gpt-oss-20b
      api_key: os.environ/OPENROUTER_KEY
  - model_name: local-coder
    litellm_params:
      model: ollama/qwen3:4b
      api_base: http://localhost:11434
  - model_name: claude-sonnet
    litellm_params:
      model: claude-sonnet-4-20250514
      api_key: os.environ/ANTHROPIC_API_KEY
```

```bash
# Launch the proxy
litellm --config litellm_config.yaml

# Connect Claude Code
export ANTHROPIC_BASE_URL="http://localhost:4000"
export ANTHROPIC_AUTH_TOKEN="litellm_master"
claude --model gpt-oss-20b
```

LiteLLM is useful when you have **multiple providers** and want a single entry point with cost logging and load balancing.

## Method 5: Official Cloud Providers

Anthropic officially supports running Claude Code through cloud providers. These are **the same Claude models**, but billed through your cloud provider:

**Amazon Bedrock:**
```bash
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1
```

**Google Vertex AI:**
```bash
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=your-project-id
```

**Microsoft Azure Foundry:**
```bash
export CLAUDE_CODE_USE_FOUNDRY=1
export ANTHROPIC_FOUNDRY_RESOURCE=your-resource
export ANTHROPIC_FOUNDRY_API_KEY=your-api-key
```

This is useful for **corporate scenarios**: billing through existing AWS/GCP/Azure contracts, compliance with security policies, access control via IAM.

## Honest Warning: Other Models Work Worse

This is important to know before you start experimenting.

**Why Claude Code works better with Claude:**

1. **Prompts are optimized for Claude** — Anthropic knows exactly how their models respond to instructions
2. **Tool calling** — Claude is trained to work with tools (file editing, git, bash). Other models may do this poorly
3. **The model is trained for agentic environments** — Claude was trained for multi-step task execution

**Typical problems with other models:**

| Problem | Example |
|---------|---------|
| Doesn't call tools | Model writes text instead of editing a file |
| Generates garbage | Empty lines and spaces instead of code |
| Ignores context | Doesn't read project files, gives generic answers |
| Breaks format | Response doesn't parse in Claude Code |

**What works and what doesn't:**

| Task | With other models | Recommendation |
|------|-------------------|---------------|
| Explain code | Good | Qwen, DeepSeek, GPT work fine |
| Answer a question | Good | Any model |
| Documentation | Good | Free models work |
| File editing | Poor | Needs a model with tool calling |
| Running commands | Poor | Needs a model with tool calling |
| Complex multi-step tasks | Poor | Use Claude |

**Practical advice:** use other models for **reading and analysis**, and Claude for **actions** (editing, running, git).

## Comparison of Approaches

| Approach | Complexity | Cost | Privacy | Quality |
|----------|-----------|------|---------|---------|
| **OpenRouter** | Simple | From $0 (free models) | Medium | Depends on model |
| **Claude Code Router** | Medium | From $0 | Medium | Smart routing |
| **Ollama** | Simple (`ollama launch`) | $0 (needs hardware) | Maximum | Limited by model size |
| **LiteLLM** | Complex | Depends on providers | Medium | Flexible |
| **Bedrock/Vertex/Foundry** | Medium | Pay-as-you-go | High (corporate) | Claude (full quality) |

## How to Get Started

**Simplest path: OpenRouter + free model**

```bash
# 1. Register at openrouter.ai and get a key
# 2. Set variables
export ANTHROPIC_BASE_URL="https://openrouter.ai/api"
export ANTHROPIC_AUTH_TOKEN="or_your_key"
export ANTHROPIC_API_KEY=""

# 3. Launch with a free model
claude --model qwen/qwen3-coder:free
```

**For maximum privacy: Ollama**

```bash
# 1. Install Ollama
brew install ollama

# 2. Launch Claude Code with one command (downloads model automatically)
ollama launch claude

# Or manually:
ollama pull qwen3-coder
export ANTHROPIC_BASE_URL="http://localhost:11434"
export ANTHROPIC_AUTH_TOKEN="ollama"
claude --model qwen3-coder
```

**For advanced users: Claude Code Router**

```bash
# 1. Install the router
npm install -g @musistudio/claude-code-router

# 2. Configure providers (config.json)
# 3. Launch
ccr start && ccr ui
```

## Alternative: Cline and Kilo Code When Claude Code is Unavailable

There are situations when Claude Code can't be installed or used directly — for example, corporate policies block CLI tools, there's no access to the Anthropic API, or your organization restricts installing third-party terminal applications.

In such cases, excellent alternatives are **Cline** and **Kilo Code** — AI agents that work as VS Code extensions:

- **[Cline](https://github.com/cline/cline)** — open-source agent that can autonomously read and edit files, run terminal commands, and work with the browser
- **[Kilo Code](https://kilocode.ai/)** — a Cline fork with additional modes (Code, Architect, Ask, Debug) and improved context management

![Kilo Code in VS Code with Claude Sonnet model via OpenRouter](img/kilocode.png)

**Key advantage:** these extensions support connecting through **OpenRouter**, which provides access to Claude models (and hundreds of others) via a single API. This means you can use the same Claude Sonnet and Opus models, but through a VS Code extension instead of a terminal agent.

**How to set up:**

1. Install the [Cline](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev) or [Kilo Code](https://marketplace.visualstudio.com/items?itemName=kilocode.kilo-code) extension from VS Code Marketplace
2. Register at [OpenRouter](https://openrouter.ai/) and get an API key
3. In the extension settings, select OpenRouter as the provider and enter your API key
4. Choose a model — for example, `anthropic/claude-sonnet-4` or a free alternative

| Scenario | Solution |
|----------|---------|
| Corporation blocks CLI tools | Cline/Kilo Code as VS Code extension |
| No access to Anthropic API | OpenRouter as proxy to Claude models |
| No API budget | Free models through OpenRouter |
| Need agentic approach in VS Code | Kilo Code with Code/Architect/Debug modes |

## Module Summary

- Claude Code can be connected to **any** OpenAI-compatible model via `ANTHROPIC_BASE_URL`
- **OpenRouter** — the simplest path to 400+ models, including free ones
- **Claude Code Router** — smart proxy with task-to-model routing
- **Ollama** — local models for privacy and offline work
- **LiteLLM** — single proxy for mixed configurations
- **Bedrock/Vertex/Foundry** — official providers for corporate use
- Other models work worse — prompts and tools are optimized for Claude
- Best strategy: other models for **analysis and questions**, Claude for **actions and complex tasks**
- If Claude Code is unavailable — use **Cline** or **Kilo Code** in VS Code with OpenRouter

---

[← Previous: Best Practices](09-best-practices.md) | [Back to Course](README.md)
