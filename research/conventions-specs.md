# Conventions & Coding Standards Documentation for AI Tools

Research into what makes conventions/coding-standards documentation useful vs harmful when consumed by AI coding assistants. Conducted 2026-02-26.

## Core Finding

Conventions documentation has become essential infrastructure for AI coding assistants, but only when it captures knowledge the AI cannot infer from code or linter configuration. Documenting linter-enforceable rules or restating standard language idioms wastes tokens and degrades performance. The highest-value conventions are human-judgment decisions: architectural patterns, library choices, and the "why" behind non-obvious practices.

## Key Evidence

### Conventions docs measurably improve AI agent efficiency

A controlled study across 10 repositories and 124 pull requests found that AGENTS.md files containing coding conventions, architecture descriptions, and project structure reduced median completion time by 28.6% and median output tokens by 16.6%. The files gave agents enough context to avoid exploratory dead-ends.
- Source: https://arxiv.org/html/2601.20404v1

A larger empirical study of 2,303 context files across 1,925 repositories found that the most common content categories were testing procedures (75%), implementation details/coding style (69.9%), architecture (67.7%), and build/run commands (62.3%). These functional categories dominate because they directly affect agent behaviour.
- Source: https://arxiv.org/html/2511.12884v1

### But only for non-inferable knowledge

Anthropic's official CLAUDE.md guidance draws a sharp line. Include: commands the AI cannot guess, style rules that differ from defaults, architectural decisions, environment quirks, common gotchas. Exclude: anything inferable from code, standard language conventions, what config files already express, directory structures, platitudes like "write clean code." The self-test: "Would removing this cause Claude to make mistakes?" If not, cut it.
- Source: https://code.claude.com/docs/en/best-practices

GitHub Copilot's documentation warns against "requests meant to generally and non-specifically improve behavior" such as "Be more accurate" or "Identify all issues" --- these add noise that confuses the model.
- Source: https://docs.github.com/en/copilot/concepts/code-review/coding-guidelines

### Linter-enforceable rules vs human-judgment conventions

The 2010s saw written coding conventions decline as linters like ESLint and Prettier automated low-level style enforcement. Teams stopped maintaining prose documents about formatting. But linters enforce the letter of style rules, not the spirit or higher-level design conventions. AI coding assistants have reversed this trend: teams need explicit documentation of architectural patterns, component organisation, state management approaches, and design rationale that automated tooling cannot enforce.
- Source: https://www.brokenrobot.xyz/blog/the-renaissance-of-written-coding-conventions/

JetBrains argues that guidelines should capture judgments that automated tools cannot enforce --- while linters handle syntax, guidelines preserve institutional knowledge about architectural decisions, design patterns, and context-specific tradeoffs. The recommendation is to include concrete code examples of good vs bad patterns, not abstract principles.
- Source: https://blog.jetbrains.com/idea/2025/05/coding-guidelines-for-your-ai-agents/

When AI-generated code ignores team patterns, architecture, or naming conventions, developers rewrite or reject it even when it is technically correct. Qodo's 2025 survey found developers who receive inconsistent output are 1.5x more likely to flag "code not in line with team standards" as a top frustration.
- Source: https://www.qodo.ai/reports/state-of-ai-code-quality/

### Common failure modes

**Bloated convention files get ignored.** Anthropic identifies the "over-specified CLAUDE.md" as a common failure: when the file is too long, important rules get lost in the noise. If Claude keeps violating a rule despite it being documented, the file is probably too long and the rule is buried.
- Source: https://code.claude.com/docs/en/best-practices

**Lost in the middle.** LLM attention is not evenly distributed. Information at the beginning and end of context has the most impact; middle content is deprioritised. Critical conventions buried mid-document are architecturally likely to be ignored (RoPE positional encoding, not a prompting failure).
- Source: https://arxiv.org/abs/2307.03172

**Cursor rules get selectively applied.** Cursor's AI uses rules "if they seem relevant to the current query" but deprioritises them when deemed unrelated. Overly broad rules dilute the signal. Splitting into context-specific .mdc files that activate only when relevant produces better adherence than a single monolithic file.
- Source: https://docs.cursor.com/context/rules-for-ai

**Vague guidance is not actionable.** Broad statements like "use consistent patterns" or "follow best practices" do not translate into constraints an agent can act on. Effective conventions are specific: "Use kebab-case for URL paths" not "use consistent naming."
- Source: https://blog.thepete.net/blog/2025/05/22/why-your-ai-coding-assistant-keeps-doing-it-wrong-and-how-to-fix-it/

**Single-file manifests don't scale.** A study of a 108,000-line C# system found that convention files must separate always-loaded "hot memory" (660 lines of core conventions) from on-demand "cold memory" (34 specification documents, 16,250 lines). A single file cannot describe a large system. The project's context infrastructure was 24.2% of total codebase lines.
- Source: https://arxiv.org/html/2602.20478

**Stale conventions cause silent failures.** The primary failure mode in the codified-context study was specification staleness: outdated documentation caused agents to generate syntactically correct but functionally incorrect code that only surfaced during testing.
- Source: https://arxiv.org/html/2602.20478

### What tool makers recommend

**Anthropic (CLAUDE.md):** Keep concise and human-readable. Use emphasis ("IMPORTANT", "YOU MUST") to improve adherence. Check into git. Prune regularly. Use Skills for domain-specific knowledge that does not belong in every session.
- Source: https://code.claude.com/docs/en/best-practices

**GitHub Copilot:** Short, self-contained statements. Max ~1,000 lines per instruction file. Imperative directives over narrative paragraphs. Path-specific instruction files for conventions that only apply to certain directories.
- Source: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

**Cursor:** Split rules into context-specific .mdc files. Use ONLY/NEVER for rules with no exceptions; softer framing for judgment calls. Tokens used for instructions reduce available context for code understanding.
- Source: https://docs.cursor.com/context/rules-for-ai

**JetBrains (Junie):** Provide a "living catalog" of conventions. Include technology-specific guidelines, dos and don'ts, common pitfalls, and concrete code examples. Can auto-generate initial guidelines from existing codebase.
- Source: https://blog.jetbrains.com/idea/2025/05/coding-guidelines-for-your-ai-agents/

**Augment Code:** Codify patterns (like `user_*` for DB models) once, then auto-enforce across repositories. Keep guidelines in version-controlled `.augment-guidelines` file so the team shares the same rules.
- Source: https://www.augmentcode.com/guides/enterprise-coding-standards-12-rules-for-ai-ready-teams

### Readability and format findings

The empirical study of 2,303 agent context files found median length of 485-535 words, but readability was poor: median Flesch Reading Ease score of 16.6 (comparable to dense legal documents). Files behave as living configuration, not static documentation --- 67.4% of Claude Code files were modified across multiple commits, with updates every 1-3 days in short bursts driven by incremental additions.
- Source: https://arxiv.org/html/2511.12884v1

Addy Osmani recommends selective inclusion: provide only the portions relevant to the task and explicitly tell the AI what not to focus on. Show in-line examples of desired patterns by pointing to existing code in the codebase rather than describing them in prose.
- Source: https://addyosmani.com/blog/ai-coding-workflow/

## Implications for Writing Conventions Specs

1. **Only document decisions the AI cannot infer.** Linter rules, standard idioms, and anything visible in config are already available. Repeating them wastes tokens and may degrade performance.

2. **Separate always-needed conventions from domain-specific ones.** A short, high-signal core file (under 500 lines) should cover universal project conventions. Domain or directory-specific rules belong in separate files loaded on demand.

3. **Be concrete and specific.** "Use opentelemetry over logging" is actionable. "Use appropriate observability" is not. Include brief good/bad examples where the convention is non-obvious.

4. **Front-load critical rules.** Positional attention bias means the first and last items in a conventions file get the most weight. Put the most-violated or most-important rules first.

5. **Maintain aggressively.** Stale conventions cause agents to generate plausible but wrong code. A wrong convention is worse than no convention. Prune rather than accumulate.

6. **Format for machine consumption.** Tables, lists, and imperative directives. Not narrative prose. Not preamble. Not rationale paragraphs (link to separate docs for the "why" if needed).

7. **Use enforcement hierarchy.** Linter rules > CI hooks > conventions file instructions. Anything enforceable by tooling should be enforced by tooling, not documented as a hope.
