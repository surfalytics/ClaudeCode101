# Module 9: Best Practices for Working with Claude Code

> Based on: [code.claude.com/docs/en/best-practices](https://code.claude.com/docs/en/best-practices)

## The #1 Rule

Most best practices come down to one thing: **the context window fills up fast, and quality drops as it fills**.

The context window stores your entire conversation: messages, file contents, command output. A single debugging session can consume tens of thousands of tokens. When the window is full, Claude starts "forgetting" early instructions and making more mistakes.

All tips below are ways to manage context effectively.

## 1. Give Claude a Way to Verify Its Work

This is the **most important** tip. Claude works much better when it can verify results itself: run tests, compare screenshots, check output.

Without verification, you're the only feedback. With verification, Claude catches its own mistakes.

| Approach | Bad | Good |
|----------|-----|------|
| Verification criteria | "write an email validation function" | "write validateEmail. Tests: user@example.com → true, invalid → false. Run tests after implementation" |
| UI changes | "make the dashboard prettier" | "[paste screenshot] implement this design. Take a screenshot of the result and compare with the original" |
| Bug fixes | "the build fails" | "the build fails with error: [error]. Fix it and verify the build passes. Find the root cause, don't suppress the error" |

## 2. First Explore, Then Plan, Then Code

If Claude jumps straight to coding, it might solve the wrong problem. Separate work into phases:

**Phase 1: Research (Plan Mode)**

Switch to Plan Mode (`Shift+Tab` twice). Claude only reads files and answers questions, changing nothing.

```
> Read src/auth/ and understand how we handle sessions.
```

**Phase 2: Planning**

```
> I want to add Google OAuth. Which files need to change?
  What will the flow look like? Make a plan.
```

**Phase 3: Implementation (Normal Mode)**

Switch back and let Claude code per the plan:

```
> Implement the OAuth flow per your plan.
  Write tests for the callback handler, run them and fix errors.
```

**Phase 4: Commit**

```
> Commit with a good description and create a PR.
```

**When to skip planning:** if the task is simple (fix a typo, add a log, rename a variable) — just ask Claude to do it. Planning is for unclear tasks, multi-file changes, or unfamiliar code.

## 3. Be Specific in Your Prompts

The more precise the prompt, the fewer iterations.

| Strategy | Bad | Good |
|----------|-----|------|
| Limit the scope | "add tests for foo.py" | "write a test for foo.py covering the case when the user is not logged in. No mocks" |
| Specify sources | "why does ExecutionFactory have such a weird API?" | "look at the git history of ExecutionFactory and explain how its API evolved to its current state" |
| Reference patterns | "add a calendar widget" | "look at how existing widgets are built. HotDogWidget.php is a good example. Build the calendar following the same pattern" |
| Describe symptoms | "fix the login bug" | "login fails after session timeout. Check src/auth/, especially token refresh. Write a test that reproduces the bug, then fix it" |

**Provide rich context:**

- **@file** — reference files with @, Claude will read them
- **Screenshots** — paste images directly into the prompt (drag & drop)
- **URLs** — provide links to documentation
- **Pipe** — `cat error.log | claude` to send file contents

## 4. Manage Your Session

**Interrupt and correct early:**

Don't wait for Claude to finish if you see it going the wrong way:

- **Esc** — stop Claude. Context is preserved, you can redirect
- **Esc + Esc** or **`/rewind`** — roll back to the previous state (both code and conversation)
- **"Undo that"** — Claude will revert its changes
- **`/clear`** — full context reset for a new task

**Two-attempt rule:** if you've corrected Claude twice on the same topic and it still doesn't get it — the context is polluted with failed attempts. Do `/clear` and start over with a better prompt.

**Manage context aggressively:**

```
/clear          — reset between tasks
/compact        — compress context, keeping the important parts
/compact focus on the API changes   — compress with focus
/context        — see what's taking up space
```

**Use sub-agents for research:**

Sub-agents work in a **separate context** and return only a brief summary:

```
> Use a sub-agent to investigate how our authentication system
  handles token refresh and whether there are ready-made OAuth utilities.
```

The sub-agent will read dozens of files, but your main context only gets the summary. This is one of the most powerful tools for preserving context.

**Resume conversations:**

```bash
claude --continue    # Continue the last conversation
claude --resume      # Choose from recent ones
```

Use `/rename` to give sessions descriptive names: "oauth-migration", "memory-leak-debugging".

## 5. Set Up Your Environment

**Write a good CLAUDE.md:**

Run `/init` — Claude will create an initial CLAUDE.md based on your project.

What to include:
- Build and test commands that Claude wouldn't guess
- Code style rules that differ from standard
- Testing instructions
- Architectural decisions
- Environment variables

What NOT to include:
- What Claude will understand from the code itself
- Standard language conventions
- Detailed API documentation (link instead)
- Description of every file

**Keep CLAUDE.md short** — up to ~500 lines. If longer, Claude starts ignoring rules. Move reference materials to Skills.

**Use CLI tools:**

Claude works great with CLI tools:

- **`gh`** (GitHub CLI) — creating PRs, issues, reviews
- **`aws`**, **`gcloud`** — cloud work
- **`sentry-cli`** — error monitoring

```
> Use 'gh issue list' to find open bugs
  and fix the most critical one
```

Claude can also **learn** new CLI tools on the fly:

```
> Use 'foo-tool --help' to understand the tool,
  then solve task A with it
```

**Configure permissions:**

Use `/permissions` to allow safe commands once and for all:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(git status)",
      "Bash(ruff check *)"
    ]
  }
}
```

## 6. Scale Your Work

**Headless mode for automation:**

```bash
# One-time request
claude -p "explain what this project does"

# Structured output for scripts
claude -p "list all API endpoints" --output-format json

# Streaming for real-time processing
claude -p "analyze the log file" --output-format stream-json
```

**Writer/Reviewer pattern:**

Use two sessions — one writes code, the other reviews:

| Session A (Writer) | Session B (Reviewer) |
|---------------------|-------------------|
| "Implement a rate limiter for the API" | |
| | "Review the rate limiter implementation in src/middleware/. Find edge cases and race conditions" |
| "Here's feedback: [output from B]. Fix it" | |

A fresh reviewer context is unbiased — it's not attached to the code it wrote itself.

**Fan-out for bulk operations:**

```bash
# Migrate 2000 files
for file in $(cat files.txt); do
  claude -p "Migrate $file from React to Vue. Return OK or FAIL." \
    --allowedTools "Edit,Bash(git commit *)"
done
```

## 7. Common Mistakes

**"Junk drawer session"** — you started with one task, asked about something else, then came back to the first. Context is cluttered. **Solution:** `/clear` between unrelated tasks.

**"Endless corrections"** — Claude does it wrong, you correct, wrong again. Context is poisoned with failed attempts. **Solution:** after two failures — `/clear` and a new, better prompt.

**"Bloated CLAUDE.md"** — a 1000-line file. Claude ignores half of it. **Solution:** ruthlessly cut. If Claude already does it right without the instruction — remove it.

**"Endless exploration"** — you asked Claude to "figure it out" without limits. It read hundreds of files and filled the context. **Solution:** limit scope or use sub-agents.

**"Trust but don't verify"** — Claude wrote code that looks correct but doesn't handle edge cases. **Solution:** always provide a way to verify (tests, scripts). If you can't verify — don't deploy.

## 8. Develop Your Intuition

These tips are starting points, not dogmas. Sometimes you **need** to let context accumulate because you're deep in a complex problem. Sometimes a vague prompt is exactly what you need to see how Claude interprets the task.

Pay attention to what works. When Claude delivers a great result — notice what you did: prompt structure, context, mode. When Claude stalls — ask yourself why. Context too noisy? Prompt too vague? Task too large for a single pass?

Over time, you'll develop an intuition that no guide can describe.

## Module Summary

- **Most important** — give Claude a way to verify its work (tests, screenshots, criteria)
- **Context is the main resource.** Use `/clear`, `/compact`, sub-agents to manage it
- **Work phases:** research → planning → implementation → commit
- **Specific prompts** = fewer iterations. Specify files, patterns, criteria
- **CLAUDE.md** — short and to the point, up to 500 lines
- **Interrupt early** — Esc, `/rewind`, `/clear` instead of long sessions with accumulated errors
- **Scale up:** headless mode, writer/reviewer, fan-out for bulk operations

---

[← Previous: MCP & Extensions](08-mcp-and-skills.md) | [Back to Course](README.md) | [Next: Other Models →](10-other-models.md)
