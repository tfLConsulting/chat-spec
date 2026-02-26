# Writing Effective CLAUDE.md Files

What makes CLAUDE.md files effective, based on research. See `ai-doc-quality.md` for general principles; this covers what's specific to AI config files.

## What to Include

| Content | Good | Bad |
|---------|------|-----|
| Commands with flags | `uv run pytest -v --tb=short` | "run the tests" |
| Non-default conventions | "Use `Result[T]`, never raise" | "follow clean architecture" |
| Critical-rule emphasis | `IMPORTANT: never commit .env` | burying it mid-file |
| `@path` imports | `@docs/auth-flow.md` | inlining 200 lines |
| Gotchas from real failures | "`db:migrate` before `test:int`" | "be careful with DB" |
| Stack with versions | "React 18, TS 5.4, Vite 6" | "React project" |

Explicit tool mentions increase agent usage 2.5x.

## What to Leave Out

| Content | Why |
|---------|-----|
| Architecture overviews | Agents ignore them (AGENTbench) |
| `/init` or LLM-generated content | -2 to -3% vs no file |
| Linter-enforceable rules | Let tooling handle it |
| File trees, directory maps | Agent discovers by exploring |
| Negative instructions | "use X" beats "don't use Y" |

## Structure

**50-150 lines.** At 200+, adherence degrades. Claude Code's system prompt uses ~50.

| Layer | Location | Loaded |
|-------|----------|--------|
| Core | `CLAUDE.md` (root) | Always |
| Detail | `docs/*.md` via `@path` | When referenced |
| Skills | `.claude/skills/*.md` | Agent-triggered (unreliable --- 44% in one eval) |

Critical knowledge goes in CLAUDE.md or `@imports`, not Skills.

## Self-Check

1. Under 150 lines?
2. Would removing any line cause a mistake? If not, cut it.
3. Every command accurate right now?
4. Positive instructions, not negative?
5. Built from observed failures, not speculation?
6. Could a linter enforce it instead?

Research backing these principles is in `research/claude-md-best-practices.md`.
