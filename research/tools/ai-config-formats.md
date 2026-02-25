# AI Config File Formats

Overview of per-project instruction files for AI coding assistants.

## The Fragmentation

Every tool has its own format. A 217k-repo GitHub survey found CLAUDE.md is the most adopted (45.4% of repos with any AI config), followed by AGENTS.md (40.6%).

## Format Details

### CLAUDE.md (Claude Code)

- **Locations:** `CLAUDE.md` (root), `CLAUDE.local.md` (personal, gitignored), `~/.claude/CLAUDE.md` (user-level), subdirectory `CLAUDE.md` (on-demand), `.claude/rules/*.md` (modular rules)
- **Hierarchy:** Parent dirs load in full at launch. Child dirs load on-demand. Most specific wins.
- **Strengths:** Hierarchical system is the most sophisticated. Well-documented.
- **Limitations:** Instructions lose importance over long conversations (by 4th-5th interaction, Claude may ignore rules). Long files get partially skipped. No validation.
- **Resources:** [Official docs](https://code.claude.com/docs/en/memory), [Anthropic blog](https://claude.com/blog/using-claude-md-files), [Builder.io guide](https://www.builder.io/blog/claude-md-guide)

### .cursorrules / .cursor/rules/*.mdc (Cursor)

- **Locations:** `.cursorrules` (legacy, deprecated), `.cursor/rules/*.mdc` (modern)
- **MDC format:** YAML frontmatter with `description`, `globs` (file pattern triggers), `alwaysApply`
- **Community:** [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules) — 163+ curated files. [dotcursorrules.com](https://dotcursorrules.com/).
- **Limitations:** If both formats exist, .mdc takes precedence (confusing). Invalid YAML silently dropped. Character limits undocumented.
- **Resources:** [Official docs](https://docs.cursor.com/context/rules-for-ai)

### .github/copilot-instructions.md (GitHub Copilot)

- **Locations:** `.github/copilot-instructions.md` (repo-wide), `.github/instructions/*.instructions.md` (path-specific with YAML frontmatter), `.github/prompts/*.prompt.md` (reusable prompts as slash commands)
- **Limitations:** Instructions not visible in Chat view. No feedback on conflicts. Whitespace ignored.
- **Resources:** [Official docs](https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot), [VS Code guide](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)

### .windsurfrules (Windsurf)

- **Locations:** `.windsurfrules` (legacy), `.windsurf/rules/*.md` (modern)
- **Limits:** 6,000 chars/file, 12,000 total. Content silently truncated.
- **Activation modes:** Manual (@mention), Model Decision, Glob, Always
- **Resources:** [Official docs](https://docs.windsurf.com/windsurf/cascade/memories), [Rules Directory](https://windsurf.com/editor/directory)

### CONVENTIONS.md (Aider)

- **Location:** `CONVENTIONS.md` loaded via `--read` flag or `.aider.conf.yml`
- **Marked read-only, cached for prompt efficiency.**
- **Resources:** [Aider docs](https://aider.chat/docs/usage/conventions.html)

### GEMINI.md (Gemini CLI)

- **Locations:** `~/.gemini/GEMINI.md` (user-level), `GEMINI.md` in project root and subdirs
- **Special:** Supports `@./path/to/file.md` import syntax. `/memory` command for interactive management.
- **Resources:** [Official docs](https://google-gemini.github.io/gemini-cli/docs/cli/gemini-md.html)

### .clinerules (Cline)

- **Locations:** `.clinerules` (flat file) or `.clinerules/` directory with modular markdown
- **Philosophy:** Treats instructions as code — commit to git, review in PRs
- **Resources:** [Cline blog](https://cline.bot/blog/clinerules-version-controlled-shareable-and-ai-editable-instructions)

### Other

- `.replit.md` — Replit agent instructions
- `llms.txt` / `llms-full.txt` — Website-level LLM docs ([llmstxt.org](https://llmstxt.org/)), analogous to robots.txt

## What works (community consensus)

1. Structured, scannable content (bullets > prose)
2. Concrete code examples ("one snippet beats three paragraphs")
3. Reference existing files rather than duplicating
4. Add rules only when you notice recurring problems, not speculatively
5. Executable commands with flags (`npm run test -- --coverage` not "run tests")
6. Clear boundaries (what agent should NOT do)
7. Keep files short and focused

## What's missing (community complaints)

1. No validation or linting (Agnix is an early attempt)
2. No feedback loop — can't tell if instructions were followed
3. Degradation over long conversations
4. No package-level context (proposed `CONTEXT.md` for npm/pip packages)
5. Drift across tools
6. Over-engineering tendency
7. No composability standard

## Sources

- [217k repo survey](https://alteredcraft.com/p/mapping-ai-agent-adoption-across)
- [Martin Fowler on context engineering](https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html)
- [Canterbury study](https://arxiv.org/html/2602.14690v1)
- [0xdevalias comprehensive notes](https://gist.github.com/0xdevalias/f40bc5a6f84c4c5ad862e314894b2fa6)
- [EveryDev on fragmentation](https://www.everydev.ai/p/blog-ai-coding-agent-rules-files-fragmentation-formats-and-the-push-to-standardize)
