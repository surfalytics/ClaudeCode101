# Module 5: More Claude Code Examples

## Introduction

In this module, we'll look at different scenarios where Claude Code can help. Each example is a real-world task that can be solved in minutes.

## Example 1: Web Scraper for Data Collection

```
Create a Python script news_scraper.py that:
1. Uses requests and beautifulsoup4 libraries
2. Downloads the Hacker News front page
3. Extracts news titles, links, and scores
4. Saves the result to hacker_news.csv
```

## Example 2: File Automation

```
Create a script file_organizer.py that:
1. Takes a folder path as an argument
2. Creates subfolders by file type
3. Moves files to the corresponding subfolders
4. Supports a --dry-run flag
```

## Example 3: REST API in 5 Minutes

```
Create a REST API on FastAPI for managing notes:
- CRUD operations
- SQLite via SQLAlchemy
- Dockerfile and docker-compose.yml
```

## Example 4: Data Analysis and Report

```
Create a script sales_report.py:
- Generate test data
- Analyze sales
- Create 4 charts (2x2 grid)
- Save to sales_dashboard.png
```

## Example 5: Telegram Bot

```
Create a Telegram bot in Python:
- /start, /weather, /currency, /joke
- Tokens from environment variables
```

## Example 6: Refactoring Existing Code

```
Analyze all Python code and:
- Find issues, add type hints, docstrings, logging
- Create a report refactoring_report.md
```

## Example 7: Database Utilities

```
Create a utility db_migrator.py:
- Reads data from SQLite
- Export to CSV, JSON, Parquet
- Interactive menu
```

## Example 8: Testing

```
For the stock_analysis.py project:
- Tests with pytest using mocks
- conftest.py with fixtures
```

## Example 9: Documentation

```
Create a complete README.md, add docstrings, CHANGELOG.md
```

## Summary Table

| # | Task | Difficulty | Time* | Key Skills |
|---|------|-----------|--------|------------|
| 1 | Web scraper | Simple | 5 min | requests, BS4, CSV |
| 2 | File organizer | Simple | 5 min | os, argparse |
| 3 | REST API | Medium | 15 min | FastAPI, SQLAlchemy, Docker |
| 4 | Sales dashboard | Medium | 10 min | pandas, matplotlib |
| 5 | Telegram bot | Medium | 15 min | API, bot framework |
| 6 | Refactoring | Medium | 10 min | Code analysis, PEP 8 |
| 7 | DB utility | Medium | 10 min | SQLite, CLI |
| 8 | Tests | Medium | 10 min | pytest, mocking |
| 9 | Documentation | Simple | 5 min | Markdown, docstrings |

*Approximate time with Claude Code. Without it, each task would take 3-5x longer.*

## Practical Tips

1. **Iterative approach** — don't try to describe everything in one prompt
2. **Verify at each step** — run and test
3. **Project context** — give Claude Code more information
4. **CLAUDE.md file** — create project rules
5. **Working with errors** — just paste the error to Claude Code

## Module Summary

- Specificity — the more precise the prompt, the better the result
- Iterations — break tasks into steps
- Verification — always check generated code
- Context — give Claude Code information about your project

---

[← Previous: Hands-on: Stock Analysis](04-stock-analysis.md) | [Back to Course](README.md) | [Next: AI Code Quality →](06-code-quality.md)
