# Dev-Context Documentation for AI-Assisted Development

Research into what practitioners, tool makers, and researchers recommend for structuring project-level context files that orient AI coding agents (and new developers) to a codebase. Conducted 2026-02-26.

## Core Finding

Less is more, and the evidence is now empirical. The ETH Zurich AGENTbench study (Feb 2026) tested 138 real repositories and found that LLM-generated context files *decreased* agent performance by 2-3%, while even developer-written files only improved it by 4%. The mechanism: agents are highly compliant with instructions in context files, but broadened exploration from those instructions rarely translates into higher success rates and often entangles agents in unnecessary requirements. The only content that reliably helped was information that cannot be discovered from the code itself.

## Key Evidence

### The AGENTbench study (ETH Zurich, Feb 2026)

The first controlled empirical study of context file effectiveness. Tested four frontier coding agents (Claude Code Sonnet 4.5, GPT-5.2, GPT-5.1 mini, Qwen3-30b-coder) across 138 repository instances derived from 5,694 real GitHub pull requests.

Key metrics:
- LLM-generated AGENTS.md files: **-2 to -3% performance**, +20% inference cost, +2-4 tool steps per task
- Developer-written files: **+4% performance**, but still +20% cost
- Agents spent 14-22% more reasoning tokens processing documentation, reducing capacity for actual problem-solving
- Stronger models (GPT-5.2) did not benefit more; they already had enough parametric knowledge that extra context was redundant noise
- When existing README files were removed, LLM-generated context suddenly helped (+2.7%), confirming the core problem is duplication

Recommendation from the study: include only information that cannot be discovered from the code itself. Start with an empty file and add instructions only after observing repeated agent mistakes.

- Source: [Evaluating AGENTS.md (arXiv)](https://arxiv.org/html/2602.11988)
- Source: [Summary by Umesh Malik](https://umesh-malik.com/blog/agents-md-ai-coding-agents-study)
- Source: [Upsun analysis](https://devcenter.upsun.com/posts/agents-md-less-is-more/)

### Developer-provided context taxonomy (arXiv, Dec 2025)

Empirical study of 401 open-source repositories containing Cursor rules, identifying five categories of developer-provided context: project information (35%), guidelines (33%), conventions (15%), examples (9%), and LLM directives (8%). Static-typed languages (Go, C#, Java) received less context since type systems provide inherent constraints; dynamic languages (JavaScript, PHP) required more. Critically, the study notes that the actual impact on LLM performance "remains an open question" and that "excessive context may even degrade performance if it confuses the LLM."

- Source: [An Empirical Study of Developer-Provided Context (arXiv)](https://arxiv.org/html/2512.18925v1)

### Context gaps are the top quality problem (Qodo, 2025)

Survey of developers found that 65% report AI tools miss critical context during important tasks (refactoring, test generation, code review). Context pain increases with seniority: 41% of juniors vs 52% of seniors report it. When context is manually selected, 54% say AI still misses relevance; with autonomous context selection this drops to 33%; with persistent cross-session context it drops to 16%.

- Source: [Qodo State of AI Code Quality 2025](https://www.qodo.ai/reports/state-of-ai-code-quality/)
- Source: [Qodo press release](https://www.prnewswire.com/news-releases/despite-78-claiming-productivity-gains-two-in-three-developers-say-ai-misses-critical-context-according-to-qodo-survey-302480084.html)

### Codified context as infrastructure (arXiv, Feb 2026)

Case study of a 108,000-line C# system built across 283 development sessions with 2,801 human prompts. Proposes a three-tier knowledge architecture: hot memory (a ~660-line constitution loaded every session), specialist agents (19 domain experts, ~9,300 lines invoked per task), and cold memory (34 specification documents, ~16,250 lines retrieved on demand). The knowledge infrastructure represented 24.2% of total codebase size. Key principle: "if you explained it twice, write it down." Treats documentation as load-bearing infrastructure that AI agents depend on to produce correct output.

- Source: [Codified Context: Infrastructure for AI Agents in a Complex Codebase (arXiv)](https://arxiv.org/html/2602.20478)

## What AI Tool Makers Recommend

### Anthropic (Claude Code)

Official guidance centres on a self-test: for each line in CLAUDE.md, ask "Would removing this cause Claude to make mistakes?" If not, cut it. Files that are too long cause Claude to ignore instructions.

**Include:** Bash commands Claude cannot guess, code style rules that differ from defaults, testing instructions, architectural decisions, dev environment quirks, common gotchas.

**Exclude:** Anything inferable from code, standard conventions, detailed API docs (link instead), information that changes frequently, file-by-file codebase descriptions, self-evident practices ("write clean code").

**Structure:** No required format, but keep it short and human-readable. Use `@path/to/file` imports to reference detailed docs without inlining them. Place CLAUDE.md files hierarchically: root for project-wide, subdirectories for folder-specific, `~/.claude/CLAUDE.md` for personal defaults. Use Skills (`.claude/skills/`) for domain knowledge that is only sometimes relevant. Use Hooks for rules that must apply deterministically, not advisorily.

**Size guidance:** ~150-200 instructions is the practical ceiling for consistent following. If Claude keeps doing something despite a rule against it, the file is probably too long. Anthropic explicitly warns: "Bloated CLAUDE.md files cause Claude to ignore your actual instructions!"

- Source: [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

### GitHub Copilot

Uses `.github/copilot-instructions.md` for repository-wide context, with path-specific `.instructions.md` files using glob patterns. Also recognises AGENTS.md. Instructions should be "not task specific" but provide enduring context. Five recommended sections: project overview ("elevator pitch"), tech stack, coding guidelines, project structure, available resources.

**Size limit:** No longer than about 1,000 lines. Quality deteriorates beyond this.

**Key advice:** "An imperfect instructions file will deliver far more impact than nothing at all." Start minimal, iterate based on what works.

- Source: [GitHub Docs: Adding custom instructions](https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot)
- Source: [GitHub Blog: 5 tips for better custom instructions](https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/)

### Cursor

Deprecated monolithic `.cursorrules` in favour of individual `.cursor/rules/*.mdc` files (Markdown with YAML frontmatter). Supports four activation modes: Always, Auto Attached, Agent Requested, and Manual. This enables progressive disclosure: always-on rules stay small, detailed rules load only when relevant.

**Size limit:** Individual rule files under 500 lines. Focused, composable rules over monolithic files. Reference files with `@filename` rather than inlining content.

- Source: [Cursor IDE Rules for AI](https://kirill-markin.com/articles/cursor-ide-rules-for-ai/)
- Source: [EveryDev: AI Agent Rule Files](https://www.everydev.ai/p/blog-ai-coding-agent-rules-files-fragmentation-formats-and-the-push-to-standardize)

### Windsurf

Supports AGENTS.md for directory-scoped instructions that automatically apply based on file location. Distinguishes AGENTS.md (simple, location-based) from Rules (more control over activation logic). Recommends bullet points and lists over prose, NEVER/ALWAYS flags for critical rules, and no generic rules ("write good code") since those are already in training data.

- Source: [Windsurf AGENTS.md Docs](https://docs.windsurf.com/windsurf/cascade/agents-md)

### AGENTS.md (open standard, Aug 2025)

A cross-tool standard for AI agent context, supported by 20+ tools including Claude Code, Copilot, Cursor, Codex, Gemini, Zed, and Windsurf. Uses standard Markdown with no required fields. Follows proximity rules (nearest file in directory tree takes precedence) and supports monorepo nesting. Intended as a complement to README.md, not a replacement.

- Source: [agents.md specification](https://agents.md/)

## Splitting Context: Multiple Files vs One Big File

The evidence consistently favours splitting over monolithic files, but with important caveats.

**The ~150-line threshold.** Multiple practitioners and the ETH Zurich study converge on a practical limit: files over approximately 150 lines show diminishing returns. Frontier thinking models can follow ~150-200 instructions with reasonable consistency; smaller models degrade much faster.

**Progressive disclosure is the key pattern.** Rather than loading everything upfront, structure context in layers: a lightweight index/routing layer that tells the agent what exists and where to find it, detailed content loaded only when relevant, and deep reference material retrieved on demand. Claude Code's Skills architecture implements this pattern: skills load metadata at startup, require activation before full content loads, and access supporting files on demand.

**The three-layer pattern appears independently across multiple sources:**

| Layer | What it contains | When loaded | Size guidance |
|-------|-----------------|-------------|---------------|
| Always-on | Identity, universal conventions, critical rules, routing table | Every session | 50-150 lines |
| Project-specific | Stack, architecture, commands, current focus | Per-project session | 50-150 lines |
| On-demand | Glossaries, specs, reference material, domain knowledge | When relevant | No strict limit |

**Practical splitting advice:**
- Keep root context file under 300 lines, ideally 50-150
- Use import syntax (`@path/to/file`) to reference details without inlining
- Tell the AI how to find information rather than including all of it (progressive disclosure)
- Different team members can own different rule files without merge conflicts
- Per-directory context files prevent cross-project context bleed in monorepos

- Source: [Builder.io: How to Write a Good CLAUDE.md File](https://www.builder.io/blog/claude-md-guide)
- Source: [DEV Community: Designing a CLAUDE.md Context System](https://dev.to/syntora/designing-a-claudemd-context-system-how-i-give-ai-full-project-context-without-re-explaining-3p2p)
- Source: [Honra: Progressive Disclosure for AI Agents](https://www.honra.io/articles/progressive-disclosure-for-ai-agents)

## Common Failure Modes and Anti-Patterns

### Content that actively hurts

| Anti-pattern | Mechanism | Evidence |
|-------------|-----------|----------|
| Restating what code says | Duplicates existing documentation; agents spend tokens re-reading what they already know | AGENTbench: removing READMEs made LLM-generated context helpful (+2.7%) |
| Verbose, comprehensive prose | Fills context window, triggers attention degradation | Chroma context rot research; AGENTbench +14-22% reasoning tokens wasted |
| LLM-generated context files | Tend to duplicate existing docs; decreased performance 2-3% | AGENTbench (2026) |
| Generic guidance ("write clean code") | Already in training data; wastes tokens without changing behaviour | Windsurf, Anthropic guidance |
| Codebase overviews | Agents discover structure naturally from file exploration | AGENTbench, Upsun analysis |

### Content that drifts and misleads

| Anti-pattern | Mechanism | Evidence |
|-------------|-----------|----------|
| Stale architecture docs | Agent builds on false premises; "coding against fiction" | Moss (2025): agent-guard pre-commit hook |
| Aspirational documentation | Describes intended design, not actual state; agent implements the fiction | Practitioner reports |
| Undated context | No way to judge freshness; AI treats all text as equally current | Common in cursorrules analysis |

### Structural problems

| Anti-pattern | Mechanism | Evidence |
|-------------|-----------|----------|
| Kitchen-sink files (>300 lines) | Rules lost in noise; AI follows some, ignores others | Anthropic: "Bloated CLAUDE.md files cause Claude to ignore your actual instructions!" |
| Instruction overload (>200 rules) | Omission errors increase; 500 instructions drops best model to 62.8% accuracy | arXiv:2507.11538 |
| Scattered definitions | When chunked for retrieval, individual chunks become ambiguous | kapa.ai writing best practices |
| Multiple conflicting formats | Each tool has its own format; duplication creates drift risk | EveryDev fragmentation analysis |

## The Fragmentation Problem

As of early 2026, the ecosystem has no unified standard. Developers maintaining context for multiple tools must write and maintain separate files: CLAUDE.md, `.cursor/rules/*.mdc`, `.github/copilot-instructions.md`, AGENTS.md, `.windsurfrules`, and more. Emerging standardisation efforts (AGENTS.md, MCP, llms.txt) show convergence signals but have not yet eliminated the duplication burden.

- Source: [EveryDev: AI Agent Rule Files Chaos](https://www.everydev.ai/p/blog-ai-coding-agent-rules-files-fragmentation-formats-and-the-push-to-standardize)
- Source: [AGENTS.md specification](https://agents.md/)
- Source: [llms.txt specification](https://llmstxt.org/)

## What Actually Works: A Synthesis

Combining the empirical evidence, tool-maker guidance, and practitioner experience, effective dev-context documentation has these properties:

1. **Contains only non-discoverable information.** If the agent can find it by reading code, config files, or package manifests, do not repeat it. The AGENTbench study is unambiguous: duplication hurts.

2. **Is concise and high-density.** Every line competes for attention budget. The practical ceiling is ~150-200 instructions for consistent following. Bullet points and imperative statements outperform prose.

3. **Uses progressive disclosure.** A small always-loaded core (50-150 lines) routes to detailed on-demand content. The agent should know *where* to find information, not have all of it pre-loaded.

4. **Focuses on gotchas, not descriptions.** What is non-obvious? What will the agent get wrong without being told? What conventions differ from common defaults? What commands are required but unguessable?

5. **Stays accurate or gets deleted.** Stale docs are worse than no docs. Documentation drift is "the silent killer of AI-assisted development." Treat context files as load-bearing infrastructure requiring regular maintenance.

6. **Is built iteratively from observed failures.** Start empty. Add a rule only when an agent makes the same mistake more than once. Never generate comprehensive context files upfront.

7. **Provides verification methods.** Include test commands, linting steps, and expected outputs. Anthropic calls this "the single highest-leverage thing you can do" for AI-assisted development.

## Implications for chat-spec

Dev-context documentation (CLAUDE.md, AGENTS.md, cursorrules, etc.) is a new documentation category that did not exist before 2024. It differs from traditional developer documentation in several ways:

- **Primary audience is a machine**, not a human (though humans read it too)
- **Brevity is a measurable quality**, not just a preference (token costs, attention degradation)
- **Accuracy has immediate, observable consequences** (agent builds on false premises within minutes)
- **Completeness is a liability**, not an asset (AGENTbench proves this empirically)
- **Structure matters for retrieval**, not just readability (progressive disclosure, activation modes)

Any rubric for this category should weight conciseness and accuracy far above completeness. The additive scoring approach (more content = higher score) is precisely wrong for this document type.
