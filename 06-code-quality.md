# Module 6: Model Quality and AI-Generated Code

> More about models: [docs.anthropic.com/en/docs/about-claude/models](https://docs.anthropic.com/en/docs/about-claude/models) | [Model config in Claude Code](https://code.claude.com/docs/en/model-config)

## Introduction

AI agents write code fast. But fast does not mean good. In this module we will look at how to evaluate code quality from Claude Code, which models handle different tasks better, and how to set up your process so that the result is production-ready.

## Not All Code is Created Equal

When you ask Claude Code to write a script, it will produce working code in seconds. But "working" and "quality" are two different things.

### Example: a simple HTTP request

Bad code (but it works):

```python
import requests
r = requests.get("https://api.example.com/data")
d = r.json()
for i in d:
    print(i["name"])
```

Quality code:

```python
import logging
from typing import Any

import requests
from requests.exceptions import HTTPError, ConnectionError, Timeout

logger = logging.getLogger(__name__)

def fetch_data(url: str, timeout: int = 30) -> list[dict[str, Any]]:
    """
    Fetches data from an API.

    Args:
        url: API endpoint address.
        timeout: Request timeout in seconds.

    Returns:
        List of records from the API.

    Raises:
        ConnectionError: If the server is unreachable.
        HTTPError: If the server returns an error.
    """
    try:
        response = requests.get(url, timeout=timeout)
        response.raise_for_status()
        data = response.json()
        logger.info("Loaded %d records from %s", len(data), url)
        return data
    except ConnectionError:
        logger.error("Failed to connect to %s", url)
        raise
    except Timeout:
        logger.error("Timeout when requesting %s", url)
        raise
    except HTTPError as e:
        logger.error("HTTP error %s: %s", e.response.status_code, url)
        raise
```

Both versions do the same thing. But the second one is reliable, readable, and ready for production.

## Model Comparison: What to Use and When

### Claude Models

| Model | Speed | Code Quality | Price | Best For |
|-------|-------|-------------|-------|----------|
| Claude 3.5 Haiku | Very fast | Medium | $ | Simple tasks, quick fixes |
| Claude 3.5 Sonnet | Fast | Good | $$ | Daily work |
| Claude 4 Sonnet | Fast | Very good | $$ | Complex tasks, refactoring |
| Claude 4 Opus | Slow | Excellent | $$$$ | Architecture, complex logic |

### How to Choose a Model

**Use Haiku / fast models for:**
- Renaming variables
- Simple scripts
- Code formatting
- Generating test data

**Use Sonnet for:**
- Writing new functions
- Refactoring
- Writing tests
- Working with APIs

**Use Opus for:**
- Designing architecture
- Complex algorithms
- Multi-file refactoring
- Code review and security analysis
- Debugging tricky bugs

### Switching Models in Claude Code

```bash
# Launch with a specific model
claude --model claude-sonnet-4-20250514

# Inside a session
/model claude-sonnet-4-20250514
/model claude-opus-4-20250514
```

## Common Quality Issues

### 1. Code Hallucinations

AI can "invent" libraries or functions that do not exist.

**Example of the problem:**

```python
# Claude wrote
from pandas import smart_merge  # This function does NOT exist in pandas!
```

**How to protect yourself:**
- Always run the code after generation
- Check imports -- do these modules actually exist?
- Use a virtual environment -- import errors become immediately visible

**Prompt defense:**

```text
Use ONLY standard functions from pandas 2.1+.
Do not use deprecated API. Before each import, make sure
the module exists.
```

### 2. Missing Error Handling

AI often writes the "happy path" -- code that only works when everything goes well.

**Prompt defense:**

```text
Add handling for all possible errors:
- What if the file is not found?
- What if the API doesn't respond?
- What if the data is empty?
- What if the data format is wrong?
```

### 3. Security Issues

AI can write code with vulnerabilities:

```python
# DANGEROUS: SQL injection
query = f"SELECT * FROM users WHERE name = '{user_input}'"

# SAFE: parameterized query
query = "SELECT * FROM users WHERE name = ?"
cursor.execute(query, (user_input,))
```

**Prompt defense:**

```text
Code must be secure:
- Never insert user input into SQL directly
- Use parameterized queries
- Validate all input data
- Do not store secrets in code
```

### 4. Over-Engineering

```python
# Claude wrote 50 lines with classes and patterns
# for a task that can be solved in 5 lines

class DataProcessorFactory(AbstractFactory):
    ...
```

**Prompt defense:**

```text
Write the simplest possible solution. No classes if functions
will do. No design patterns if the task is simple.
```

### 5. Outdated Approaches

```python
# Outdated approach
import os
file_path = os.path.join("data", "file.csv")

# Modern approach
from pathlib import Path
file_path = Path("data") / "file.csv"
```

**Prompt defense:**

```text
Use modern Python 3.12+ approaches:
- pathlib instead of os.path
- f-strings instead of format()
- dataclasses or pydantic instead of plain classes
- match/case instead of long if/elif chains
```

## Code Quality Checklist

### Basic Level (always check)

- [ ] **Code runs** -- no errors when executing
- [ ] **Code does what's needed** -- result matches the task
- [ ] **No hardcoded secrets** -- passwords, tokens, keys not in code
- [ ] **Dependencies exist** -- all imports are real

### Intermediate Level (for projects)

- [ ] **Type hints** -- all functions have argument and return types
- [ ] **Docstrings** -- all functions are documented
- [ ] **Error handling** -- try/except where needed
- [ ] **Logging** -- logging instead of print
- [ ] **Tests** -- at least basic tests exist

### Advanced Level (for production)

- [ ] **Linter passes** -- ruff/flake8 with no errors
- [ ] **Formatting** -- code formatted (black/ruff format)
- [ ] **Security** -- no SQL injection, XSS, etc.
- [ ] **Performance** -- no obvious issues (N+1 queries, memory leaks)
- [ ] **Edge cases** -- boundary cases handled

## How to Make Claude Code Write Quality Code

### Method 1: Detailed Prompt

```text
Write a function to process a CSV file.

Quality requirements:
- Type hints for all arguments and return values
- Docstring in Google format
- Error handling: file not found, empty file, wrong format
- Logging via the logging module
- Tests in a separate file
- Code must pass ruff without errors
```

### Method 2: CLAUDE.md File

```markdown
# Code Quality Rules

## Required
- Type hints for all functions
- Docstrings in Google format
- Handle all errors (try/except)
- logging instead of print
- Tests for each new function

## Style
- Maximum line length: 100 characters
- Import sorting: isort
- Formatting: black
- Linter: ruff

## Security
- Never hardcode secrets
- Parameterized SQL queries
- Validate all input data
```

### Method 3: Two-Step Approach

Step 1 -- generation:

```text
Write a function for parsing logs
```

Step 2 -- review:

```text
Now check this code for:
1. Error handling
2. Type hints
3. Security
4. Performance
5. Edge cases

Fix all issues found.
```

### Method 4: Automated Checking

```text
After writing the code:
1. Run ruff check on all changed files
2. Run ruff format
3. Run mypy for type checking
4. Run pytest
5. Fix all issues found
```

## Model Benchmarks: Real Numbers

### SWE-bench (fixing real bugs in open-source projects)

| Model | Result | Comment |
|-------|--------|---------|
| Claude 4 Opus | ~72% | Best result among all models* |
| Claude 4 Sonnet | ~65% | Excellent price/quality balance |
| Claude 3.5 Sonnet | ~49% | Good for everyday tasks |
| GPT-4o | ~38% | Lags behind in coding benchmarks |

*Data is approximate and may change with new versions.*

### HumanEval (writing functions from descriptions)

Most modern models score 85-95% on this benchmark. The differences between models become apparent on more complex tasks.

### In Practice

Benchmarks are one thing, but real-world work is another. In reality, code quality depends on:

1. **Prompt quality** -- 40% of the result
2. **Model choice** -- 30% of the result
3. **Project context** -- 20% of the result (CLAUDE.md, structure)
4. **Iterations** -- 10% of the result (review and fixes)

## When NOT to Trust AI Code

### Red Flags

- **Cryptography** -- never write your own cryptography via AI. Use proven libraries.
- **Financial calculations** -- AI may make rounding and order-of-operations errors.
- **Medical/legal systems** -- code that affects lives or people's rights.
- **Security** -- authentication, authorization, encryption -- verify manually.

### What to Check Manually

```text
Before merging to main, ALWAYS check:

1. SQL queries -- no injection
2. Input data -- all validated
3. Secrets -- none leaked into code
4. Edge cases -- empty data, null, very large values
5. Concurrency -- no race conditions
```

## Quality Metrics

### How to Measure AI-Generated Code Quality

| Metric | How to Measure | Good Value |
|--------|---------------|------------|
| Linter | ruff check | 0 errors |
| Typing | mypy | 0 errors |
| Test coverage | pytest --cov | >80% |
| Complexity | radon | A or B rating |
| Security | bandit | 0 high severity |

### Automating Checks

Ask Claude Code to set up automated checks:

```text
Set up pre-commit hooks for the project:
- ruff (linter + formatting)
- mypy (type checking)
- pytest (tests)
- bandit (security)

Create a .pre-commit-config.yaml file and a Makefile
with commands: make lint, make test, make check
```

## Practical Exercise

### Task: Improve Code Quality

1. Ask Claude Code to write a simple script:

```text
Write a script that downloads JSON from an API,
processes the data, and saves it to CSV
```

2. Evaluate the result using the quality checklist above

3. Ask Claude Code to improve the code:

```text
Check this code against the quality checklist.
Add everything that's missing: type hints, docstrings,
error handling, logging, tests.
```

4. Compare the first and second version -- the difference will be noticeable

## Module Summary

- Code quality from AI depends on prompt quality and model choice
- Opus for complex tasks, Sonnet for everyday work, Haiku for quick fixes
- Always check generated code: run it, test it, review it
- A CLAUDE.md file with quality rules is your best friend
- The two-step approach (generation + review) gives the best results
- Don't trust AI in critical areas: security, cryptography, finance
- Automated checks (linter, tests, types) catch most issues

---

[← Previous: More Examples](05-more-examples.md) | [Back to Course](README.md) | [Next: Agent Army →](07-agent-army.md)
