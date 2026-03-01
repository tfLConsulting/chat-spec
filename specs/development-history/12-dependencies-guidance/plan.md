# Plan: Dependencies Guidance

## Context

Dependencies is the first Tier 3 specialized artifact. `package.json` / `requirements.txt` / `go.mod` says *what*; the guidance targets what's invisible in lockfiles — the *why* behind choices, constraints that drove selection, known limitations, and upgrade/migration context that agents need when suggesting changes. Without this, AI agents recommend replacements for libraries chosen for specific reasons, suggest upgrades blocked by known incompatibilities, or duplicate functionality already provided by an existing dependency.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why dependencies next

- First Tier 3 artifact — most universally applicable (every project with a package manager)
- Highest-impact failure mode: agents suggest adding dependencies that duplicate existing ones, or upgrading past known-broken versions
- `package.json` says what; the *why* behind choices is the most common undocumented tribal knowledge
- Dependency docs are the most prone to bloat — listing every package with its description restates what `npm info` provides

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/dependency-specs.md` | Research findings on dependency documentation for AI | — |
| `guidance/dependencies.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches dependency documentation for AI, writes to `research/dependency-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/dependencies.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 12 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — 1973 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
