# chat-spec

Iterative documentation quality tracking for AI-assisted projects.

## The Problem

Your AI assistant doesn't know your project. It guesses at architecture, invents conventions, and hallucinates API details. Adding a CLAUDE.md helps, but keeping it accurate and comprehensive is a manual chore nobody does.

## What chat-spec Does

chat-spec is a protocol your AI assistant follows to evaluate your project's documentation, identify gaps, and iteratively improve it — one session at a time.

It tracks what exists, scores it against rubrics, and proposes focused improvements. You approve, it generates. Every session moves the needle.

No installation. No dependencies. Just a protocol your AI reads and follows.

## Quickstart

Paste this into any AI assistant:

> Read https://github.com/lindow-consulting/chat-spec/blob/main/PROTOCOL.md and follow the chat-spec protocol for this project.

Or add it permanently to your AI config file:

**Claude Code** — add to `CLAUDE.md`:
```
Follow the chat-spec protocol at https://github.com/lindow-consulting/chat-spec/blob/main/PROTOCOL.md to evaluate and improve this project's documentation.
```

**Cursor** — add to `.cursorrules`:
```
Follow the chat-spec protocol at https://github.com/lindow-consulting/chat-spec/blob/main/PROTOCOL.md to evaluate and improve this project's documentation.
```

Then ask your AI to "run chat-spec."

## How It Works

1. **First run:** Your AI scans the project, creates a manifest tracking which docs exist and their quality scores, and generates your first spec improvement.

2. **Every run after:** Your AI reads the manifest, identifies the biggest gap or most stale doc, and proposes one focused improvement.

3. **You control it:** The AI always proposes before acting. You approve, redirect, or skip.

State lives in `.chat-spec/manifest.yaml` — a small YAML file tracking scores, backlog, and history. Commit it to git to share with your team.

## What You Get

chat-spec tracks these documentation types (and more):

| Type | What it covers |
|------|---------------|
| Architecture | System structure, components, data flow |
| Features | What the project does, user capabilities |
| Conventions | Coding standards, patterns, testing expectations |
| Dev Context | Setup, key decisions, gotchas |
| API Docs | Endpoints, schemas, authentication |
| + 8 more | Deployment, data model, security, and more |

Each type has a rubric — a checklist of specific, measurable quality criteria. Your AI scores docs against these rubrics and shows you exactly what's missing.

## FAQ

**Does it work with my AI tool?**
Designed to work with any AI tool that can read a URL — Claude Code, Cursor, GitHub Copilot, and others.

**Will it overwrite my existing docs?**
No. It always proposes changes for your approval. It builds on what you have.

**How long does a session take?**
Most sessions are 2-5 minutes. First run is longer (5-10 minutes).

**Do I need to run it regularly?**
Most useful after significant code changes or when onboarding someone new. No schedule required.

**Is the generated documentation any good?**
chat-spec's spec-writing rules are grounded in research showing that AI-generated docs which restate the obvious actually hurt AI performance. The protocol explicitly avoids this — it focuses on non-obvious information that adds genuine value.

## Links

- [PROTOCOL.md](./PROTOCOL.md) — the full protocol (what your AI reads)
- [RUBRICS.md](./RUBRICS.md) — scoring rubrics for all artifact types
