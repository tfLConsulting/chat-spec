# GitHub Spec Kit

- **Repo:** [github/spec-kit](https://github.com/github/spec-kit)
- **Stars:** 71,900 | **Forks:** 6,196 | **Issues:** 625
- **Language:** Python | **License:** MIT
- **Created:** 2025-08-21 | **Last active:** 2026-02-24

## What it does

GitHub's official SDD toolkit. Positions specs as executable artifacts that generate implementations. The spec becomes the single source of truth for both humans and AI agents.

## How it works

Two components:
1. **Specify CLI** (`pip install specify-cli`) — scaffolds `.specify/` directory with templates for `spec.md`, `plan.md`, and `tasks/`
2. **Templates & Slash Commands** — pre-built prompts integrating with coding agents via `/specify`, `/plan`, `/tasks`

Four-gated-phase workflow:
1. **Specify** — describe goals, user journeys, constraints in `spec.md`
2. **Plan** — declare architecture, stack, dependencies in `plan.md`
3. **Tasks** — agent breaks plan into small, reviewable task files
4. **Implement** — agent executes tasks one at a time

Each phase has explicit checkpoints requiring human approval.

## Supported AI tools (18+)

Claude Code, GitHub Copilot, Cursor, Gemini CLI, Windsurf, Qwen Code, Roo Code, Qoder CLI, Amazon Q Developer, Amp, Auggie, CodeBuddy, Codex, IBM Bob, Jules, Kilo Code, opencode, plus generic.

## Strengths

- Massive community and GitHub institutional backing
- Multi-agent support across 18+ tools
- Spec-first prevents "vibe coding" drift
- Open source (MIT)

## Criticisms

- **"Reinvented Waterfall"** — Scott Logic test found it generated 2,500+ lines of markdown per feature, making development ~10x slower than iterative prompting
- **Agents ignore instructions** — even with templates and checklists, agents frequently deviate mid-implementation
- **Spec evolution is hard** — when requirements change, reading multiple spec versions undermines single source of truth
- **Overkill for small tasks** — four-phase process too heavy for bug fixes, minor features
- **Markdown is not formal** — specs aren't testable or verifiable the way code is
- **Code reuse blindness** — agents prefer creating new code over reusing existing modules

## Relevance to chat-spec

Spec Kit is about **writing specs before coding**. Chat-spec is about **evaluating and improving the project's AI context over time**. Different problems. Spec Kit doesn't track maturity, doesn't score quality, doesn't iteratively improve. However, the criticisms of Spec Kit (too heavy, waterfall) are warnings for our design — keep it lightweight and incremental.

## Sources

- [GitHub Blog announcement](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Scott Logic critique](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)
- [Martin Fowler on SDD tools](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Microsoft Developer Blog](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
