# Packmind

- **Repo:** [PackmindHub/packmind](https://github.com/PackmindHub/packmind)
- **Stars:** 214 | **Last active:** 2026-02-25 (actively maintained)

## What it does

Context engineering and governance tool for AI coding agents. Includes a `context-evaluator` that:

- Analyzes git repos for AI coding agent documentation quality
- Reads CLAUDE.md, AGENTS.md, Copilot rules, skills files
- Detects **vagueness, contradictions, gaps, missing feedback loops, and drift**
- Compares documentation coverage against actual codebase
- Runs prompts through supported agents (Claude Code, Cursor, Copilot, OpenCode, Codex) to test whether instructions are actually followed

## Why this is the closest match to chat-spec

Packmind is the only tool that **evaluates the quality of AI context documentation**. It doesn't just generate — it assesses what's there and identifies problems. This is conceptually very close to chat-spec's "evaluate" phase.

## What Packmind doesn't do (that chat-spec would)

- No persistent manifest tracking maturity over time
- No rubric-based scoring per artifact
- No iterative improvement loop (examine → evaluate → propose)
- No templated artifact generation for gaps
- Doesn't maintain a `specs/` directory — it evaluates config files, not structured knowledge

## Sources

- [Packmind GitHub](https://github.com/PackmindHub/packmind)
- [Packmind: Evaluate AI coding agent context](https://packmind.com/evaluate-context-ai-coding-agent/)
