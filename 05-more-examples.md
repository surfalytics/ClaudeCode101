# Module 5: More Claude Code Examples

## Introduction

In this module, we'll look at different scenarios where Claude Code can help. Each example is a real-world task that can be solved in minutes.

## Example 1: Web Scraper for Data Collection

### Task
Collect news headlines from a news site and save them to CSV.

### Prompt for Claude Code

```
Create a Python script news_scraper.py that:
1. Uses the requests and beautifulsoup4 libraries
2. Downloads the Hacker News front page (https://news.ycombinator.com)
3. Extracts news titles, links, and scores
4. Saves the result to hacker_news.csv
5. Prints the top 10 news items in a nice format
```

### What Claude Code Will Create

Claude Code will write a complete script with error handling, HTML parsing, and data saving. You just need to run it.

### What This Example Teaches
- Working with HTTP requests
- HTML parsing
- Saving data to CSV
- Claude Code can write data collection scripts

## Example 2: File Automation

### Task
Organize files in a folder: sort by type and date.

### Prompt for Claude Code

```
Create a script file_organizer.py that:
1. Takes a folder path as a command-line argument
2. Scans all files in that folder
3. Creates subfolders by file type (images/, documents/, videos/, other/)
4. Moves files to the corresponding subfolders
5. Creates a report.txt with a list of all moved files
6. Supports a --dry-run flag for a test run without moving
```

### What This Example Teaches
- Working with the file system
- Command-line arguments (argparse)
- Claude Code understands system tasks

## Example 3: REST API in 5 Minutes

### Task
Create a simple REST API for notes.

### Prompt for Claude Code

```
Create a REST API on FastAPI for managing notes:

Note model:
- id (auto)
- title (string)
- content (text)
- created_at (datetime)
- tags (list of strings)

Endpoints:
- GET /notes — list all notes
- GET /notes/{id} — single note
- POST /notes — create a note
- PUT /notes/{id} — update a note
- DELETE /notes/{id} — delete a note
- GET /notes/search?q=text — search by title and content

Use SQLite via SQLAlchemy.
Add a Dockerfile and docker-compose.yml.
Add example requests to the README.
```

### What Claude Code Will Do

1. Create the project structure:
```
notes-api/
├── main.py
├── models.py
├── database.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── README.md
```

2. Write all the code
3. Run the application to verify
4. Test the endpoints

### What This Example Teaches
- Claude Code can create complete projects
- Working with FastAPI, SQLAlchemy, Docker
- API design

## Example 4: Data Analysis and Report

### Task
Analyze a CSV file with sales data and create a beautiful report.

### Prompt for Claude Code

```
Create a script sales_report.py that:

1. Generates test sales data for 12 months:
   - Date, Product (5 products), Category, Quantity, Price, Region
   - 1000 records

2. Analyzes the data:
   - Total revenue by month
   - Top products by sales
   - Sales by region
   - Month-over-month growth

3. Creates 4 charts on one figure (2x2):
   - Line chart: revenue by month
   - Bar chart: sales by product
   - Pie chart: region share
   - Heatmap: products × months

4. Saves:
   - Charts to sales_dashboard.png
   - Summary table to sales_summary.csv
```

### What This Example Teaches
- Generating test data
- Pandas for analysis
- Matplotlib/Seaborn for visualization
- Claude Code as an analyst's tool

## Example 5: Telegram Bot

### Task
Create a simple Telegram bot.

### Prompt for Claude Code

```
Create a Telegram bot in Python (python-telegram-bot) that:
1. Responds to /start with a greeting
2. /weather {city} — shows weather
   (use OpenWeatherMap API)
3. /currency {USD} {EUR} — converts currency
4. /joke — sends a random joke
5. Responds to any text message with an echo

Create:
- bot.py — main file
- config.py — settings (tokens from environment variables)
- .env.example — template for environment variables
- README.md — setup and running instructions
```

### What This Example Teaches
- Working with external APIs
- Environment variables for secrets
- Claude Code can create integrations

## Example 6: Refactoring Existing Code

### Task
Improve the quality of an existing project.

### Prompt for Claude Code

```
Analyze all Python code in the current project and:

1. Find all issues:
   - Code duplication
   - Missing error handling
   - Missing type hints
   - Missing documentation
   - PEP 8 violations

2. Fix the found issues

3. Add:
   - Type hints to all functions
   - Docstrings in Google format
   - Logging instead of print
   - Error handling

4. Create a report refactoring_report.md with a list of all changes
```

### What This Example Teaches
- Claude Code can analyze entire projects
- Automated refactoring
- Improving code quality

## Example 7: Database Utilities

### Task
Create a tool for migrating data between databases.

### Prompt for Claude Code

```
Create a utility db_migrator.py that:
1. Reads data from an SQLite database
2. Shows a list of tables and their structure
3. Allows exporting any table to CSV, JSON, or Parquet
4. Shows basic statistics for each table
5. Supports SQL queries via the command line

Interface — interactive menu in the terminal.
```

### What This Example Teaches
- Working with SQLite
- Exporting data to different formats
- Interactive CLI applications

## Example 8: Testing

### Task
Write tests for an existing project.

### Prompt for Claude Code

```
For the stock_analysis.py project:
1. Create a file test_stock_analysis.py with pytest tests
2. Mock calls to yfinance (no real API requests in tests)
3. Test:
   - download_stock_data returns the correct DataFrame
   - show_statistics doesn't crash on empty data
   - Chart functions create files
4. Add conftest.py with fixtures
5. Run tests and make sure they all pass
```

### What This Example Teaches
- Writing tests with pytest
- Mocking external dependencies
- Claude Code can both write and run tests

## Example 9: Documentation

### Task
Generate documentation for a project.

### Prompt for Claude Code

```
For the current project:
1. Create a complete README.md:
   - Project description
   - Installation and setup
   - Usage examples with command screenshots
   - API documentation (if applicable)
   - How to run tests
   - Project structure
   - License

2. Add docstrings to all functions and classes

3. Create a CHANGELOG.md based on git history
```

### What This Example Teaches
- Claude Code as a documentation tool
- Automating routine tasks

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

### 1. Iterative Approach

Don't try to describe everything in one prompt. It's better to work step by step:

```
Step 1: Create the basic project structure
Step 2: Add the data model
Step 3: Add API endpoints
Step 4: Add tests
Step 5: Add Docker
```

### 2. Verify at Each Step

After each step, check the result:

```
Run the application and show that it works
```

```
Run the tests
```

### 3. Project Context

If Claude Code does something wrong, give it more context:

```
We use Python 3.12, FastAPI 0.110, SQLAlchemy 2.0.
The project uses poetry for dependency management.
All tests must be in the tests/ folder.
Use async/await for all database operations.
```

### 4. CLAUDE.md File

Create a `CLAUDE.md` file in the project root with rules:

```markdown
# Rules for Claude Code

## Technologies
- Python 3.12
- FastAPI + SQLAlchemy 2.0
- pytest for tests
- ruff for linting

## Code Style
- Type hints are required
- Docstrings in Google format
- Maximum line length: 100 characters

## Structure
- src/ — source code
- tests/ — tests
- docs/ — documentation
```

### 5. Working with Errors

If code doesn't work, just copy the error:

```
I get this error:
Traceback (most recent call last):
  File "main.py", line 45, in <module>
    app.run()
TypeError: run() missing 1 required argument: 'host'

Fix it.
```

Claude Code will read the error, find the problem, and fix it.

## Module Summary

- Specificity — the more precise the prompt, the better the result
- Iterations — break tasks into steps
- Verification — always check generated code
- Context — give Claude Code information about your project

---

[← Previous: Hands-on: Stock Analysis](04-stock-analysis.md) | [Back to Course](README.md) | [Next: AI Code Quality →](06-code-quality.md)
