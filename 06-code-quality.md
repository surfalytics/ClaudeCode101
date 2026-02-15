# Module 6: Model Quality and AI-Generated Code

> More about models: [docs.anthropic.com/en/docs/about-claude/models](https://docs.anthropic.com/en/docs/about-claude/models) | [Model config in Claude Code](https://code.claude.com/docs/en/model-config)

## Not All Code is Created Equal

AI agents write code fast. But fast doesn't mean good. "Working" and "quality" are different things.

## Model Comparison

| Model | Speed | Code Quality | Price | Best For |
|-------|-------|-------------|-------|----------|
| Claude 3.5 Haiku | Very fast | Medium | $ | Simple tasks |
| Claude 3.5 Sonnet | Fast | Good | $$ | Daily work |
| Claude 4 Sonnet | Fast | Very good | $$ | Complex tasks |
| Claude 4 Opus | Slow | Excellent | $$$$ | Architecture, complex logic |

## Common Quality Issues

1. **Code hallucinations** — AI may "invent" non-existent functions or libraries
2. **Missing error handling** — code only works on the "happy path"
3. **Security issues** — SQL injection, hardcoded secrets
4. **Over-engineering** — 50 lines with classes for a 5-line task
5. **Outdated approaches** — os.path instead of pathlib

## Code Quality Checklist

**Basic level:**
- Code runs
- Code does what's needed
- No hardcoded secrets
- Dependencies exist

**Intermediate level:**
- Type hints
- Docstrings
- Error handling
- Tests

**Advanced level:**
- Linter passes
- Security verified
- Edge cases handled

## How to Make Claude Code Write Quality Code

1. **Detailed prompts** — describe quality requirements
2. **CLAUDE.md file** — create rules once
3. **Two-step approach** — generation first, then review
4. **Automated checking** — ruff, mypy, pytest

## Model Benchmarks

| Model | SWE-bench | Comment |
|-------|-----------|---------|
| Claude 4 Opus | ~72% | Best result |
| Claude 4 Sonnet | ~65% | Excellent price/quality balance |
| Claude 3.5 Sonnet | ~49% | Good for everyday tasks |
| GPT-4o | ~38% | Lags behind in coding benchmarks |

## When NOT to Trust AI Code

- **Cryptography** — use proven libraries
- **Financial calculations** — AI may make rounding errors
- **Security** — manually verify authentication and authorization

## Module Summary

- Quality depends on the prompt and the model
- Opus for complex tasks, Sonnet for everyday work
- A CLAUDE.md file with quality rules is your best friend
- Don't trust AI in critical areas

---

[← Previous: More Examples](05-more-examples.md) | [Back to Course](README.md) | [Next: Agent Army →](07-agent-army.md)
