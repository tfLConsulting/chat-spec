# Writing Dependency Specs

Lockfiles, package manifests, and import statements express what dependencies exist. Dependency docs help only when they capture why choices were made, what must not change, and what agents get wrong.

## What to Cover

| Content | Example |
|---------|---------|
| Selection rationale | "date-fns over moment — tree-shakeable, 6KB vs 72KB, deprecated" |
| Banned alternatives with reasons | "Not `request` (abandoned 2020) — use `undici` (Node built-in from v18)" |
| Upgrade constraints with exit conditions | "Pinned to React 18 until DataGrid migration complete (issue #412)" |
| Tool preferences | "Use pnpm not npm. Use uv not pip." |
| Cross-package coupling | "aws-cdk-lib and constructs must upgrade together; mismatched versions fail silently" |
| Canonical install for confusing packages | "`pip install -U 'huggingface_hub[cli]'` not `pip install huggingface-cli` (does not exist)" |
| Platform/peer dependency gotchas | "sharp needs libvips on Alpine — `apk add vips-dev` before install" |

## What Not to Cover

| Content | Why |
|---------|-----|
| Dependency lists or version numbers | Lockfiles are source of truth; restating creates drift and wastes tokens |
| "We use React/Express/Django" | Visible in imports; zero behaviour change |
| Package README content | Agents fetch READMEs directly; duplicating rots instantly |
| "Keep dependencies updated" | Not actionable without constraints and reasons |
| Lockfile interpretation guidance | Agents should parse lockfiles with tools, not LLM reasoning |

## Self-Check

1. Does every line describe something invisible in lockfiles, manifests, or import statements?
2. Does every constraint include a reason and an exit condition?
3. Would removing any line cause an agent to suggest a wrong or dangerous package?
4. Are banned alternatives explicit ("not X, use Y, because Z") rather than implied?

Research backing these principles is in `research/dependency-specs.md`.
