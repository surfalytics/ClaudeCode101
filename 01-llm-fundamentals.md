# Module 1: Large Language Model (LLM) Fundamentals

> More about Claude models: [claude.ai/pricing](https://claude.ai/pricing) | [docs.anthropic.com/en/docs/about-claude/models](https://docs.anthropic.com/en/docs/about-claude/models)

## What is an LLM?

**LLM (Large Language Model)** is exactly what it sounds like — a large model that works with language. Simply put, it's a program that can read, understand, and generate text.

Think of it as an incredibly well-read conversation partner who has consumed billions of texts from the internet, books, and articles. It doesn't "think" like a human, but it can predict very well what word should come next in a sentence. From this simple ability come remarkable things: writing code, answering questions, translating text, and much more.

## How LLMs Work: A Simple Explanation

**Step 1: Training**

The model "reads" an enormous amount of text. While reading, it learns to find patterns:
- Which words frequently appear together
- How sentences are structured
- What logic connects ideas

It's similar to how a child learns a language: they hear a lot of speech and start understanding the rules, even without knowing the grammar.

**Step 2: Parameters**

The result of training is billions of numerical parameters (weights). Each parameter is a tiny piece of "knowledge." When people say "a model with 175 billion parameters," it means it has 175 billion such settings.

| Model | Parameters | Company |
|-------|-----------|---------|
| GPT-4 | ~1.8 trillion* | OpenAI |
| Claude 3.5 Sonnet | undisclosed | Anthropic |
| Claude 4 Opus | undisclosed | Anthropic |
| Llama 3 | 8B — 405B | Meta |
| Gemini | undisclosed | Google |

*Exact numbers for many models are not published.*

**Step 3: Text Generation**

When you ask a model a question, it doesn't search for a ready-made answer in a database. Instead, it **generates text word by word** (more precisely, token by token), each time choosing the most suitable next word.

```
Your question: "The capital of France is"
Model thinks: [Paris: 95%] [Lyon: 2%] [Marseille: 1%] ...
Answer: "Paris"
```

## Key Concepts

**Tokens** — LLMs work not with words, but with tokens. A token is a chunk of text. Examples:
- "Hello" → 1 token
- "programming" → 2-3 tokens
- "authentication" → 3-4 tokens

Why does this matter? Because every model has a **context limit** — the maximum number of tokens it can process at once.

**Context Window** — this is the model's "memory" within a single conversation.

| Model | Context Size |
|-------|-------------|
| Claude 3.5 Sonnet | 200K tokens |
| Claude 4 Opus | 200K tokens |
| GPT-4 Turbo | 128K tokens |

200K tokens is approximately 150,000 words or 500 pages of text.

**Prompt** — the text you send to the model. Prompt quality directly affects answer quality.

**Temperature** — a setting that affects the model's "creativity." Temperature 0 gives the most probable answer, temperature 1 gives a more "creative" response.

## Anthropic and Claude

**Anthropic** is the company that created Claude. It was founded in 2021 by former OpenAI employees.

| Model | Characteristic | Best For |
|-------|---------------|----------|
| Claude 3.5 Haiku | Fast and cheap | Simple tasks, chatbots |
| Claude 3.5 Sonnet | Balance of speed and quality | Daily coding work |
| Claude 4 Sonnet | Improved quality | Complex tasks, analysis |
| Claude 4 Opus | Maximum quality | The most complex tasks |

## Module Summary

- LLM is a model that predicts the next token based on context
- Answer quality depends on prompt quality
- Models have limitations: they can make mistakes and "hallucinate"
- Claude by Anthropic is one of the best models for working with code

---

[← Back to Course](README.md) | [Next: What is Claude Code →](02-what-is-claude-code.md)
