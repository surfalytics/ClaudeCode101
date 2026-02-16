# Module 1: Large Language Model (LLM) Fundamentals

> More about Claude models: [claude.ai/pricing](https://claude.ai/pricing) | [docs.anthropic.com/en/docs/about-claude/models](https://docs.anthropic.com/en/docs/about-claude/models)

## What is an LLM?

**LLM (Large Language Model)** is exactly what it sounds like — a large model that works with language. Simply put, it's a program that can read, understand, and generate text.

Think of it as an incredibly well-read conversation partner who has consumed billions of texts from the internet, books, and articles. It doesn't "think" like a human, but it can predict very well what word should come next in a sentence. From this simple ability come remarkable things: writing code, answering questions, translating text, and much more.

## How LLMs Work: A Simple Explanation

### Step 1: Training

The model "reads" an enormous amount of text. While reading, it learns to find patterns:
- Which words frequently appear together
- How sentences are structured
- What logic connects ideas

It's similar to how a child learns a language: they hear a lot of speech and start understanding the rules, even without knowing the grammar.

### Step 2: Parameters

The result of training is billions of numerical parameters (weights). Each parameter is a tiny piece of "knowledge." When people say "a model with 175 billion parameters," it means it has 175 billion such settings.

| Model | Parameters | Company |
|-------|-----------|---------|
| GPT-4 | ~1.8 trillion* | OpenAI |
| Claude 4.5 Sonnet | undisclosed | Anthropic |
| Claude 4.6 Opus | undisclosed | Anthropic |
| Llama 3 | 8B — 405B | Meta |
| Gemini | undisclosed | Google |

*Exact numbers for many models are not published.*

### Step 3: Text Generation

When you ask a model a question, it doesn't search for a ready-made answer in a database. Instead, it **generates text word by word** (more precisely, token by token), each time choosing the most suitable next word.

```
Your question: "The capital of France is"
Model thinks: [Paris: 95%] [Lyon: 2%] [Marseille: 1%] ...
Answer: "Paris"
```

## Key Concepts

### Tokens

LLMs work not with words, but with **tokens**. A token is a chunk of text. One word can be one or several tokens.

Examples:
- "Hello" → 1 token
- "programming" → 2-3 tokens
- "authentication" → 3-4 tokens

Why does this matter? Because every model has a **context limit** — the maximum number of tokens it can process at once.

### Context Window

The context window is the model's "memory" within a single conversation.

| Model | Context Size |
|-------|-------------|
| Claude 4.5 Sonnet | 200K tokens |
| Claude 4.6 Opus | 200K tokens |
| GPT-4 Turbo | 128K tokens |

200K tokens is approximately 150,000 words or 500 pages of text. This means Claude can "see" your entire project at once.

### Prompt

A **prompt** is the text you send to the model. Prompt quality directly affects the quality of the answer.

Bad prompt:
```
write code
```

Good prompt:
```
Write a Python script that downloads Microsoft stock price data
for the past year using the yfinance library, plots a closing price
chart, and saves it to a file chart.png
```

### Temperature

**Temperature** is a setting that affects the model's "creativity."

- **Temperature 0** — the model picks the most probable answer. Good for code and precise tasks.
- **Temperature 1** — the model is more "creative," may give unexpected answers. Good for text and ideas.

## What LLMs Can and Can't Do

### Does well:
- Write and explain code
- Answer questions on a wide range of topics
- Translate text
- Analyze and summarize documents
- Generate ideas and text
- Find bugs in code

### Can't do (or does poorly):
- **Precise arithmetic** — LLMs can make mistakes in complex math
- **Know recent events** — the model is trained on data up to a certain date
- **Guarantee correctness** — the model can "hallucinate" (produce plausible but incorrect information)
- **Remember between sessions** — each new conversation starts from scratch (unless special memory mechanisms exist)

## Anthropic and Claude

**Anthropic** is the company that created Claude. It was founded in 2021 by former OpenAI employees. Anthropic focuses on **AI safety** and researching how to make models more useful and less dangerous.

**Claude** is a family of models from Anthropic:

| Model | Characteristic | Best For |
|-------|---------------|----------|
| Claude 4.5 Haiku | Fast and cheap | Simple tasks, chatbots |
| Claude 4.5 Sonnet | Balance of speed and quality | Daily coding work |
| Claude 4.6 Opus | Maximum quality | The most complex tasks |

## Why is Claude Good for Programming?

1. **Large context** — 200K tokens allow working with large projects
2. **Code accuracy** — Claude understands code structure well and can write working programs
3. **Tools** — Anthropic created special tools for developers (API, Claude Code)
4. **Safety** — Claude tries not to generate malicious code

## Module Summary

- LLM is a model that predicts the next token based on context
- Answer quality depends on prompt quality
- Models have limitations: they can make mistakes and "hallucinate"
- Claude by Anthropic is one of the best models for working with code
- Claude Code is a tool that uses Claude for programming right in your terminal

---

[← Back to Course](README.md) | [Next: What is Claude Code →](02-what-is-claude-code.md)
