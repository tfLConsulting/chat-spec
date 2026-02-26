# CLAUDE.md Best Practices Research

Research into what makes CLAUDE.md files (and equivalent AI context configuration files) effective in practice. Conducted 2026-02-26.

See also: `ai-doc-quality.md` for underlying research on documentation quality, context rot, and instruction-following limits that this document builds on.

## Core Finding

The highest-performing AI context files are short, non-obvious, and human-authored. Auto-generated files actively hurt agent performance. The single most important design principle is progressive disclosure: load the minimum viable context by default, and let the agent pull in more when needed.

## Quantitative Evidence

### ETH Zurich AGENTbench (2026)

The most rigorous study to date. Researchers created AGENTbench from 138 instances across 12 Python repositories derived from 5,694 real GitHub pull requests. They tested four frontier agents (Claude Code with Sonnet 4.5, Codex with GPT-5.2 and GPT-5.1 mini, Qwen Code with Qwen3-30b-coder) under three conditions: no context files, LLM-generated files, and developer-written files.

Key results:

| Condition | Task success rate (vs baseline) | Inference cost change |
|-----------|-------------------------------|----------------------|
| Developer-written AGENTS.md | +4% | +20% |
| LLM-generated AGENTS.md | -2 to -3% | +20% |

Additional findings:
- Agents spent 14-22% more reasoning tokens when context files were present, redirecting cognitive capacity from problem-solving
- When AGENTS.md mentioned specific tools (e.g. `uv`), agents used those tools 2.5x more frequently --- the strongest positive effect
- Agents largely ignored high-level architecture overviews meant to guide file navigation
- When existing README/docs were removed, LLM-generated replacements improved performance by only 2.7%, confirming the LLM files were duplicating information already available elsewhere
- Source: https://arxiv.org/html/2602.11988v1
- Summary: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study

### Lulla et al. (2026) --- AGENTS.md efficiency impact

A separate study tested OpenAI Codex across 10 repositories and 124 pull requests using a paired within-task design with and without AGENTS.md. Human-written files with genuine project knowledge showed measurable benefits:

- Median wall-clock time: 28.64% faster (28.23 seconds reduction)
- Mean output tokens: 20.08% reduction (1,153 fewer tokens)
- Mean input tokens: 9.73% reduction
- All results statistically significant (p<0.05, Wilcoxon signed-rank test)

Critical caveat: these gains came from human-authored files containing real project knowledge. The AGENTbench study showed these gains disappear or reverse with auto-generated content.
- Source: https://arxiv.org/html/2601.20404v1

### Codified context at scale (arXiv:2602.20478)

A 283-session, 70-day case study of a three-tier context system (constitution + specialist agents + knowledge base) for a C# project:

- 108,256 lines of application code supported by 26,200 lines of context infrastructure (24.2% knowledge-to-code ratio)
- Over 50% of specialist specification content was domain facts rather than behavioral instructions
- Most-referenced specification appeared in 74 sessions without save-system bugs
- Over 80% of human prompts were under 100 words, suggesting pre-loaded context reduces explanation needs
- Knowledge base maintenance required approximately 1-2 hours weekly
- Source: https://arxiv.org/html/2602.20478

### Adoption snapshot

Approximately 5% of 466 surveyed repositories had adopted any context file format as of 2025. Content is heavily focused on functional instructions (build, testing, implementation) while non-functional concerns (performance, security) are comparatively rare.
- Source: referenced in https://arxiv.org/html/2602.20478

## What Tool Makers Officially Recommend

### Anthropic (Claude Code)

Anthropic's official guidance centres on ruthless pruning. For each line, apply the test: "Would removing this cause Claude to make mistakes? If not, cut it."

**Include:**
- Bash commands Claude cannot guess
- Code style rules that differ from defaults
- Testing instructions and preferred runners
- Repository etiquette (branch naming, PR conventions)
- Architectural decisions specific to the project
- Dev environment quirks (required env vars)
- Common gotchas or non-obvious behaviours

**Exclude:**
- Anything inferable from reading code
- Standard language conventions already in training
- Detailed API documentation (link instead)
- Information that changes frequently
- Long explanations or tutorials
- File-by-file codebase descriptions
- Self-evident practices like "write clean code"

**Structural guidance:**
- No required format; keep it short and human-readable
- Use emphasis ("IMPORTANT", "YOU MUST") to improve adherence on critical rules
- Use `@path/to/file` imports to reference external docs rather than inlining
- Place files at repo root (shared), home folder (personal global), or child directories (domain-specific)
- Check CLAUDE.md into git; it compounds in value over time
- Use Skills (`.claude/skills/`) for domain knowledge that is only sometimes relevant, rather than bloating CLAUDE.md
- ~150-200 instructions is the practical ceiling for consistent following; Claude Code's own system prompt already uses ~50

If Claude keeps doing something wrong despite having a rule, the file is probably too long and the rule is getting lost. If Claude asks questions answered in CLAUDE.md, the phrasing may be ambiguous. Treat it like code: review when things go wrong, prune regularly, test by observing behaviour.

Anthropic's internal teams use CLAUDE.md to consolidate technical knowledge, replace data catalogue tools, and onboard new engineers. Their Inference team reports 80% reduction in research time for model-specific functions using Claude with project context.
- Source: https://code.claude.com/docs/en/best-practices
- Source: https://www.anthropic.com/news/how-anthropic-teams-use-claude-code

### OpenAI (Codex / AGENTS.md)

Codex reads AGENTS.md files before doing any work. Files are discovered from global scope (`~/.codex/`) down to the current working directory, concatenated root-to-leaf. Later files override earlier guidance because they appear later in context.

- Default size limit: 32 KiB per combined set of files
- Empty files are skipped automatically
- `AGENTS.override.md` takes precedence over `AGENTS.md` at any level
- Recommended content: working agreements, preferred tools and commands, safety guidelines, directory-specific overrides
- Source: https://developers.openai.com/codex/guides/agents-md/

### GitHub Copilot

Custom instructions live in `.github/copilot-instructions.md` with support for multiple `*.instructions.md` files scoped to paths.

- Keep instructions as "short, self-contained statements that add context"
- 1,000 lines maximum; quality deteriorates beyond this
- Short imperative directives over narrative paragraphs
- Move language-specific rules to path-specific instruction files
- Source: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot

### Cursor

Migrated from monolithic `.cursorrules` to individual `.mdc` files in `.cursor/rules/`. Key principles:

- One concern per rule file; keep each small and actionable
- Scope rules to file patterns (e.g. `*.ts`) so they load only when relevant
- Token efficiency: only relevant rules activate, reducing context burden
- Source: https://dev.to/anshul_02/mastering-cursor-rules-your-complete-guide-to-ai-powered-coding-excellence-2j5h

### Windsurf

Rules defined in `.windsurf/rules/rules.md` (or legacy `.windsurfrules`):

- Bullet points and lists over prose
- NEVER/ALWAYS flags for critical rules
- No generic rules ("write good code") --- already in training
- XML tags to group related rules
- Source: https://playbooks.com/windsurf-rules

### AGENTS.md Open Standard

Contributed to the Agentic AI Foundation (Linux Foundation) in December 2025 by founding members Anthropic, OpenAI, and Block. Adopted by 60,000+ open-source projects.

- Standard Markdown; no required fields or structure
- Canonical placement at repository root; subdirectory files for monorepo scoping
- Nearest file to edited code takes precedence; explicit user prompts override everything
- Recommended sections: commands, testing, project structure, code style, git workflow, boundaries
- Source: https://agents.md/

## GitHub's Lessons from 2,500+ Repositories

GitHub analysed over 2,500 public repositories with AGENTS.md files and identified what distinguished effective files:

**Six core areas** the best files cover: commands, testing, project structure, code style, git workflow, and boundaries.

**Effective patterns:**
- Specific persona definition outperforms generic assistance ("You are a test engineer" works better than "helpful coding assistant")
- Executable commands placed in an early section with flags and options, not just tool names
- One real code snippet showing style beats three paragraphs describing it
- Explicit three-tier boundary system: always do / ask first / never do
- Tech stack specificity with versions ("React 18 with TypeScript, Vite" not "React project")

**Iteration over planning:** The best agent files grow through iteration, not upfront design. Start with a focused task, observe failures, and add rules to prevent recurrence.

**Length:** Keep files under 150 lines when possible.
- Source: https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/

## Design Principles That Work

### Progressive disclosure

The most effective architecture loads context in layers rather than all at once:

1. **Always-loaded layer** (CLAUDE.md): 50-500 tokens. Project overview, essential commands, stack summary, pointers to docs.
2. **On-demand documentation** (docs/): 200-500 tokens per file. Gotchas, domain knowledge, loaded when the agent identifies relevance.
3. **Specialist agents** (.claude/agents/): 300-800 tokens per agent. Domain experts activated only when needed.

Vercel's agent evaluations found that a compressed docs index in AGENTS.md achieved 100% pass rate, while Skills maxed at 79% and performed no better than baseline when left to trigger naturally. Skills were never invoked in 56% of test cases.
- Source: https://alexop.dev/posts/stop-bloating-your-claude-md-progressive-disclosure-ai-coding-tools/

### Positive instructions over negative

Research and practitioner experience consistently show that positive instructions outperform negative ones:

- "Only use library Y for state management" works better than "Don't use library X"
- "Apply all fixes to the existing files" works better than "Never create duplicate files"
- Negative prompts slightly reduce probabilities of unwanted tokens, but positive prompts actively boost probabilities of desired outcomes
- InstructGPT research showed models perform worse with negative prompts as they scale
- The "ironic process theory" (pink elephant paradox) may apply to LLMs: suppressing a concept can activate it

Reserve NEVER/ALWAYS for genuine safety boundaries. Reframe all other constraints as positive directives.
- Source: https://eval.16x.engineer/blog/the-pink-elephant-negative-instructions-llms-effectiveness-analysis
- Source: https://gadlet.com/posts/negative-prompting/

### Specify tools explicitly

The AGENTbench finding that tool mentions increase usage 2.5x is the strongest measurable positive effect of context files. If you want agents to use a specific tool (e.g. `uv` instead of `pip`, `pnpm` instead of `npm`), state it explicitly with the exact command.

### Non-discoverable information only

Apply the "discoverability test" to every line: can the agent figure this out by reading the code? If yes, delete it. Types of non-discoverable information that belong in context files:

- Tooling the agent cannot infer (e.g. "use `uv` for package management, not `pip`")
- Non-obvious gotchas ("Always run tests with `--no-cache` or fixtures fail")
- Custom patterns requiring preservation ("Auth module uses custom middleware; don't refactor to Express standard")
- Deprecated dependencies with live consumers ("legacy/ directory is deprecated but imported by three modules")
- Source: https://addyosmani.com/blog/agents-md/

### Write for machines, not humans

Context files should include explicit file paths, function names, parameter specifications, and "do this / don't do this" instructions rather than narrative prose. Domain facts (codebase facts, formulas, code patterns, known failure modes) are more valuable than behavioral instructions.
- Source: https://arxiv.org/html/2602.20478

## Common Anti-Patterns

### Auto-generation

Using `/init` or any LLM to generate context files produces content that duplicates what agents already discover independently. The ETH Zurich study measured a 2-3% performance decrease with LLM-generated files. Addy Osmani recommends deleting existing auto-generated files outright.
- Source: https://addyosmani.com/blog/agents-md/

### The bloated monolith

Practitioners report that overlong CLAUDE.md files cause Claude to ignore critical rules because they get lost in noise. HumanLayer recommends under 60 lines; the general consensus is under 300 lines. A 2,000-line file consumes ~1.8% of a 200K token context window before any work begins, halving available conversation turns.
- Source: https://www.humanlayer.dev/blog/writing-a-good-claude-md

### Using LLMs to do linters' jobs

Including style rules in CLAUDE.md that a linter can enforce wastes tokens and is less reliable. If ESLint, Prettier, or a type checker can enforce a rule, remove it from the context file and let the tool handle it. Use hooks for deterministic enforcement.
- Source: https://www.humanlayer.dev/blog/writing-a-good-claude-md

### Treating context files as static

Stale context files cause silent failures. Agents trust documentation absolutely and will generate code conflicting with recent refactors if the docs haven't been updated. Treat context files as load-bearing infrastructure requiring regular maintenance (the codified context study found ~1-2 hours/week for active projects).
- Source: https://arxiv.org/html/2602.20478

### Architecture overviews nobody reads

The AGENTbench study found agents largely ignored high-level architecture overviews. Directory structures, tech stack summaries, and codebase architecture descriptions are discoverable and add noise rather than value.

### Shared-by-copying

Copying instructions between tools (CLAUDE.md, .cursorrules, AGENTS.md) creates unintentional contradictions and maintenance burden. Use a single source of truth, ideally AGENTS.md as the cross-tool standard, or keep one canonical file and symlink/reference it.
- Source: https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html

## Context Engineering Principles

### Manus (production AI agent) lessons

The Manus team identified KV-cache hit rate as the single most important metric for production agents. Key architecture principles:

- Keep prompt prefix stable; even a single-token difference invalidates the cache (10x cost increase for Claude Sonnet: $0.30/MTok cached vs $3.00/MTok uncached)
- Make context append-only; avoid modifying previous actions or observations
- Ensure serialisation is deterministic
- Treat the file system as the ultimate context: unlimited, persistent, and manipulable by the agent
- Source: https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus

### Anthropic's context engineering framework

Anthropic defines the goal as "the smallest set of high-signal tokens that maximize the likelihood of your desired outcome." Four operations on context:

- **Writing context**: saving outside the context window for later retrieval
- **Selecting context**: pulling into the context window when needed
- **Compressing context**: retaining only required tokens
- **Isolating context**: splitting into separate windows (subagents)
- Source: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

## Practical Sizing Guidance

| Source | Recommended limit | Rationale |
|--------|------------------|-----------|
| Anthropic (official) | ~150-200 instructions max | Instruction-following degrades beyond this |
| HumanLayer (practitioner) | Under 60 lines | Claude system prompt uses ~50 of the budget already |
| GitHub (2,500 repos) | Under 150 lines | Excessive length wastes tokens, buries information |
| General consensus | Under 300 lines | Supported by multiple independent sources |
| OpenAI Codex | 32 KiB combined | Hard technical limit, configurable |
| GitHub Copilot | 1,000 lines max | Quality deteriorates beyond this |
| Cursor | 500 lines per rule file | Split concerns across multiple files |
| Skills (Anthropic) | 500 lines per SKILL.md | Official recommendation; split to detail files beyond |

## What Belongs in an Ideal CLAUDE.md

Based on all evidence reviewed, the ideal CLAUDE.md is:

1. **Short** (50-150 lines, under 300 at absolute maximum)
2. **Non-obvious** (nothing the agent could discover from code)
3. **Human-authored** (never auto-generated)
4. **Positively phrased** (tell the agent what to do, not what to avoid)
5. **Tool-explicit** (name exact commands with flags)
6. **Layered** (pointers to deeper docs rather than inlined content)
7. **Maintained** (reviewed and pruned regularly, treated as load-bearing infrastructure)
8. **Testable** (observe whether removing a line changes agent behaviour; if not, delete it)

Content that demonstrably helps (ranked by evidence strength):

1. Exact tool/command specifications (2.5x usage increase in AGENTbench)
2. Non-obvious gotchas and workarounds (prevents repeated failures)
3. Testing instructions with specific runners and flags
4. Custom patterns the agent must preserve
5. Build/run commands with exact syntax
6. Pointers to deeper documentation the agent should consult when relevant

Content that demonstrably hurts:

1. Architecture overviews (ignored by agents, wastes tokens)
2. Directory structure descriptions (discoverable)
3. Standard conventions already in training data
4. LLM-generated summaries of the codebase (2-3% performance decrease)
5. Verbose prose that could be bullet points
6. Stale information that contradicts current code
