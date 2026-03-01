# Plan: Design System Guidance

## Context

Design system is the second Tier 3 specialized artifact. Storybook, CSS variables, and component prop interfaces express a lot of what a design system *is*; the guidance targets what's invisible in code — composition rules, when to use which component variant, spacing/layout conventions that aren't enforced by tokens, accessibility requirements beyond default ARIA, and the *why* behind visual decisions. Without this, AI agents generate components that technically render but violate visual consistency, misuse compound components, or duplicate existing patterns with slight variations.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why design-system next

- Second Tier 3 artifact — applies to any project with a UI
- Highest-impact failure mode: agents create new components that duplicate existing ones, or compose components in ways that break visual consistency
- Storybook and CSS variables say *what*; the composition rules and variant selection logic are the most common undocumented knowledge
- Design system docs are prone to restating prop interfaces — which TypeScript already expresses

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/design-system-specs.md` | Research findings on design system documentation for AI | — |
| `guidance/design-system.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches design system documentation for AI, writes to `research/design-system-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/design-system.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 13 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — 2026 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
