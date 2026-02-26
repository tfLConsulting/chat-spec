# Writing Dev-Context Files

Dev-context (CLAUDE.md, AGENTS.md, cursorrules) targets machines. Completeness is a liability: LLM-generated context *decreases* agent performance 2-3%.

## Split by Purpose

Use focused files loaded progressively:

| File | Contains | Loaded |
|------|----------|--------|
| Root context | Critical rules, routing (`@path/to/detail`) | Always |
| Build commands | Non-guessable commands, env setup, test steps | Always |
| Decisions | Architecture choices with rationale | On demand |
| Gotchas | Things the agent gets wrong without being told | On demand |

Root file: under 150 lines. Reference details, don't inline.

## What to Include

| Content | Example |
|---------|---------|
| Unguessable commands | `pnpm test:e2e --project=api` |
| Non-default conventions | "Use `Result<T>` for service returns, never throw" |
| Hidden dependencies | "`schema.prisma` change requires `pnpm db:generate` before tests" |
| Verification steps | "After API changes: `pnpm typecheck && pnpm test`" |
| Active gotchas | "`user` has soft-delete; always filter `deleted_at IS NULL`" |

## What to Exclude

| Content | Why |
|---------|-----|
| Codebase overviews / file trees | Agents explore naturally; wastes 14-22% of reasoning tokens |
| Anything inferable from code | Duplication decreased performance 2-3% |
| Generic advice ("write clean code") | Already in training data; zero behaviour change |
| Aspirational architecture | Agent builds on fiction, not reality |
| Detailed API docs | Link instead of inlining |

## How to Build

Start empty. Add a rule only after an agent repeats a mistake.

## Self-Check

1. Would removing any line cause the agent to fail? If not, cut it.
2. Does anything duplicate code, config, or type signatures?
3. Is every statement true of the codebase *right now*?
4. Is root under 150 lines? Is each detail file one concern?
5. Built from observed failures, or written speculatively?

Research: `research/dev-context.md`.
