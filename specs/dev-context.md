# Dev Context

## Why This Exists

chat-spec solves the gap between "AI tools can generate code" and "AI tools generate *good* code for *your* project." The core bet: iterative, scored documentation tracking produces better AI context than one-shot generation or manual maintenance. The landscape research (`specs/landscape-research.md`) confirmed the scored-manifest-plus-iterative-improvement approach is novel.

## Gotchas

- **The "product" is PROTOCOL.md, not code.** There is no src/, no tests/, no build system. Quality is measured by whether AI models follow the protocol correctly, not by unit tests.
- **specs/ is overloaded.** `specs/` serves two purposes: (1) it holds chat-spec's own documentation and (2) it is the default artifact directory name that the protocol tells *other projects* to use. These are separate concerns that happen to share a name.
- **Development history is intentionally verbose.** The `specs/development-history/` tree has ~30 files from the noodling and design phases. These are kept for provenance, not for regular reading.

## Key Decisions

| Decision | Choice | Why |
|----------|--------|-----|
| Protocol, not a tool | Markdown an AI reads | Zero install friction; works with any AI tool |
| One artifact per session | Hard limit | Context windows fill up; quality degrades generating too much at once (ETH Zurich) |
| YAML manifest, not JSON | `.chat-spec/manifest.yaml` | AIs read YAML more reliably for config; humans can edit it |
| YES/NO weighted rubrics | Not Likert or LLM-as-judge | Binary scoring most reproducible across models ("Rubric Is All You Need", ACM ICER 2025) |
| Ban restating code | Section 1 of PROTOCOL.md | Restated code *hurts* AI performance and increases cost 20%+ (ETH Zurich) |
| 13 built-in types, 4 core | Not a blank slate | Opinionated defaults lower the "what do I write?" barrier; extensible |
| Thin pointer in AI config | Append to CLAUDE.md/.cursorrules | Avoids cross-tool sync; each tool reads its own config |

For the chronological decision log, see `specs/decisions.md`.

## How to "Build" and Test

No installation, no dependencies, no build step. The deliverables are three markdown files at the project root:

| File | Purpose | How to test |
|------|---------|-------------|
| PROTOCOL.md | The protocol AI tools follow | Hand it to an AI model with zero context and a test project. Does it follow the steps correctly? |
| RUBRICS.md | Scoring rubrics for 13 artifact types | Score a known artifact and check for reproducibility across models. |
| README.md | Human landing page | Does a developer understand what to do in under 60 seconds? |

Blank-slate testing is the closest thing to a test suite. Three scenarios were run with Haiku and all passed.

## History

See `specs/development-history/README.md` for the five development phases and their outcomes.

## Team

Solo maintainer project. No code review or approval process — changes land on `main` directly.

## Roadmap

- Dogfood on other real projects — the real validation that the protocol works outside its own repo
