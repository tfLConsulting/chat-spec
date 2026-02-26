# Writing Conventions Specs

Conventions docs cut AI agent task time 28.6% — but only for knowledge not inferable from code, config, or linters. Restating linter rules degrades performance.

## What Belongs

| Content type | Example |
|-------------|---------|
| Architectural pattern choices | "Zustand over Redux — single store, no providers" |
| Component/module patterns | "Colocate `*.test.ts` next to source; no `__tests__/` dirs" |
| Library-specific conventions | "Use `date-fns`, never `moment`" |
| Naming with project meaning | "DB models `user_*`; API types `UserDTO`" |
| Non-obvious error handling | "Services return `Result<T>`, never throw" |
| Test boundaries beyond config | "Integration tests hit real DB; units mock at service boundary" |

## What Does Not Belong

| Content | Why |
|---------|-----|
| Formatting (indent, quotes) | Linter/formatter enforces these |
| Standard language idioms | In training data; zero behaviour change |
| Vague guidance ("be consistent") | Agent cannot act on it |
| Aspirational rules not yet followed | Agent builds on fiction |
| Rules already in config files | Duplication hurts performance 2-3% |

## How to Write Rules

Specific imperatives, not descriptions. Test: would an agent behave differently with vs without this rule?

Good: `ALWAYS use path aliases (@/components) — never relative imports deeper than ../`
Bad: `We prefer clean imports`

## Structure for Scale

| Layer | Scope | Loaded |
|-------|-------|--------|
| Root | Universal rules, under 50 items | Always |
| Domain-specific | Rules for `api/`, `ui/`, `infra/` | On demand |

Front-load the most-violated rules — positional attention bias means mid-file content gets lost.

## Self-Check

1. Would the AI get this wrong without being told?
2. Could a linter or config file enforce this instead?
3. Is every rule actually followed in the current codebase?
4. Fewer than 50 rules in the always-loaded file?
5. Built from observed agent mistakes, not speculation?

Research: `research/conventions-specs.md`.
