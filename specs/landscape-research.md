# Landscape Research

Research conducted 2026-02-25. This documents the competitive landscape for tools that prepare projects for AI coding assistants.

## TL;DR

The space is crowded and fast-moving, but **no existing tool does what chat-spec proposes** — maintaining a scored manifest with maturity tracking that iteratively improves project AI-readiness. The closest is Packmind (214 GitHub stars), which evaluates documentation quality but doesn't maintain a persistent manifest.

## The Five Categories

### 1. Spec-Driven Development (SDD) Tools

Write specs before code. The spec becomes the contract AI codes against.

| Tool | Stars | Status | Notes |
|------|-------|--------|-------|
| **GitHub Spec Kit** | 71.9k | Active | Dominant player. 4-phase workflow (Specify→Plan→Tasks→Implement). Criticized as "reinvented waterfall" — Scott Logic found it 10x slower than iterative prompting. 2,500+ lines of markdown per feature. |
| **BMAD-METHOD** | 37.8k | Active | Multi-agent team approach (Analyst, PM, Architect, Dev, QA). Agentic agile. |
| **OpenSpec** | 25.6k | Active | Lightweight TypeScript alternative. Best for brownfield/legacy projects. |
| **Kiro** (AWS) | Closed | Preview | Full IDE (VS Code fork) with built-in SDD. 3-step: Requirements→Design→Tasks. |
| **Tessl** | Closed | Beta | Most ambitious — spec registry of 10k+ library specs to prevent API hallucinations. Detects spec-code drift. |
| **SpecPulse** | 361 | Stale | CLI-first SDD. No commits since Nov 2025. |

**Key criticism of the whole category:** Agents frequently deviate from specs mid-implementation. Spec evolution is hard. Overkill for small tasks.

### 2. AI Instruction Config Files

Per-project files that tell AI tools how to behave. The fragmented landscape:

| Format | Tool | Notes |
|--------|------|-------|
| `CLAUDE.md` | Claude Code | Hierarchical (root + subdirectory). Most adopted (45% of repos with any AI config). |
| `.cursorrules` / `.cursor/rules/*.mdc` | Cursor | Legacy→modern migration. Large community library (163+ curated rules). |
| `.github/copilot-instructions.md` | Copilot | Path-specific scoping via `.instructions.md` files. |
| `.windsurfrules` | Windsurf | 6k char/file limit, 12k total. |
| `CONVENTIONS.md` | Aider | Loaded via `--read` flag. Cached for efficiency. |
| `AGENTS.md` | Cross-tool | **Emerging standard** under Linux Foundation. Supported by 12+ tools. 60k+ repos. |
| `GEMINI.md` | Gemini CLI | Supports `@import` syntax. |
| `.clinerules` | Cline | Version-controlled, AI-editable. |

**Critical finding:** ETH Zurich study found AGENTS.md files improve performance by only ~4%, and LLM-generated ones have *negative* effect. Inference cost increases 20%+. Content quality matters far more than having a file at all.

**Cross-tool sync tools:** Ruler, ContextPilot, rulesync, ai-rules-sync, knowhub — all try to solve the "maintain one source, fan out to many formats" problem.

### 3. Codebase-to-Context Tools

Pack a repo into a single file for LLM consumption.

| Tool | Stars | Notes |
|------|-------|-------|
| **Repomix** | ~70k | Dominant. XML output, Tree-sitter compression, MCP server, Secretlint. |
| **Gitingest** | ~14k | Replace "hub" with "ingest" in GitHub URLs. Zero-install. |
| **code2prompt** | ~5.1k | Rust CLI. Handlebars templating. MCP server mode. |
| **CTX** | Small | "Context as Code" — YAML config for reproducible context generation. |

All are **one-shot** tools. None do iterative improvement.

### 4. Documentation Generators

| Tool | Status | Notes |
|------|--------|-------|
| **Google Code Wiki** | Preview | Gemini-powered, auto-regenerates on code changes. Public repos only. |
| **Swark** | Active | VS Code extension, generates Mermaid architecture diagrams via Copilot. |
| **Kodesage** | Commercial | Legacy codebase knowledge platform. Knowledge graph approach. Enterprise. |
| **Mintlify Autopilot** | Active | Monitors codebase, proposes doc updates via PRs. $300/mo Pro plan. |

### 5. Quality/Maturity Tracking (the gap chat-spec targets)

| Tool | Stars | Notes |
|------|-------|-------|
| **Packmind** | 214 | **Closest match.** `context-evaluator` tool analyzes AI config file quality — detects vagueness, contradictions, gaps. Tests if agents actually follow instructions. But no persistent manifest. |
| **Codebase Context Spec** | 137 | Proposes `.context/` directory structure. No quality scoring. Stagnant (10mo no commits). |
| **Codified Context** (paper) | N/A | Academic framework: hot-memory + cold-memory + domain agents. Research, not a tool. |

## Key Gaps No Tool Fills

1. **No scored manifest/index of documentation maturity** — nobody tracks "how good is each piece of your AI context" persistently
2. **No iterative autonomous improvement** — everything is either one-shot or requires manual triggering
3. **No systematic evaluation of what an AI agent actually needs** vs what exists
4. **Specification evolution is unsolved** — every tool struggles with keeping specs as living documents

## Notable Academic Research

- **ETH Zurich (arxiv:2602.11988):** AGENTS.md files barely help (~4%), LLM-generated ones hurt. Content quality >>> having a file.
- **Codified Context (arxiv:2602.20478):** Single-file manifests don't scale past ~1k LOC. Multi-tier context needed.
- **Canterbury (arxiv:2602.14690):** "Distinct configuration cultures forming around different tools." Claude Code users employ the broadest range.

## Sources

Full URLs preserved for reference:

- [GitHub Spec Kit](https://github.com/github/spec-kit) | [Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Scott Logic critique](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)
- [Martin Fowler on SDD tools](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Martin Fowler on context engineering](https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html)
- [Anthropic on context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [AGENTS.md spec](https://agents.md/) | [GitHub](https://github.com/agentsmd/agents.md)
- [ETH Zurich evaluation](https://arxiv.org/abs/2602.11988)
- [Codified Context paper](https://arxiv.org/html/2602.20478)
- [GitHub blog on AGENTS.md](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [Packmind](https://github.com/PackmindHub/packmind)
- [Codebase Context Spec](https://github.com/Agentic-Insights/codebase-context-spec)
- [Repomix](https://repomix.com/)
- [OpenSpec](https://github.com/Fission-AI/OpenSpec)
- [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD)
- [Kiro](https://kiro.dev/)
- [Tessl](https://tessl.io/)
- [SpecPulse](https://specpulse.xyz/)
- [Ruler](https://github.com/intellectronica/ruler)
- [ContextPilot](https://github.com/contextpilot-dev/contextpilot)
- [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules)
- [217k repo survey](https://alteredcraft.com/p/mapping-ai-agent-adoption-across)
- [Aider conventions](https://aider.chat/docs/usage/conventions.html)
