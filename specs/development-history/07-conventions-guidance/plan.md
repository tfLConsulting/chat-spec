# Plan: Conventions Spec Guidance

## Context

Conventions is a core artifact (max 18, tied with architecture). It's the type most prone to restating what linters, formatters, and framework defaults already enforce. Writers fill conventions specs with "use camelCase" and "follow REST patterns" — things the AI already knows and that actively hurt performance when documented. Writers need guidance on what conventions documentation adds value vs. what wastes context budget.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why conventions next

- Highest max score of core types (tied with architecture at 18)
- Most common source of `code_restatement` penalty triggers — conventions docs love to restate linter rules
- Rubric items like `code_style`, `patterns`, `naming` are where the "would an AI infer this?" filter matters most
- `anti_patterns` item (W:1) is routinely filled with generic advice ("avoid god objects") instead of project-specific traps

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/conventions-specs.md` | Research findings on conventions documentation for AI | — |
| `guidance/conventions.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches conventions documentation for AI, writes to `research/conventions-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/conventions.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Fix INDEX.md: currently missing `dev-context.md` entry (if not already fixed by phase 06)
- Update `specs/development-history/README.md` with phase 07 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — all 7 checks passed, 2,045 bytes |
| 5 | Final review | done |
| — | Fix INDEX.md | done (by phase 06; conventions row added in step 3) |
| — | Update dev history README | done |
