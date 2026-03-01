# Dependency Documentation for AI Coding Tools

Research into what makes dependency documentation effective or harmful when consumed by AI coding agents, and what content helps vs. hurts agent performance in dependency-related tasks. Conducted 2026-02-26.

## Core Finding

AI agents can read lockfiles, package manifests, and import statements to discover what dependencies exist and what versions are installed --- but they cannot infer why a dependency was chosen over alternatives, what constraints prevent upgrading, what packages are banned or preferred, or what cross-ecosystem gotchas exist. The critical gap is not *what* dependencies are present (agents read that from code) but *why* specific choices were made, *what must not change*, and *what the agent's own failure modes are*. Documenting dependency lists or version numbers restates what lockfiles express. Documenting selection rationale, banned alternatives, upgrade constraints, and known hallucination risks fills gaps that prevent costly agent mistakes.

## Key Evidence

### AI agents systematically suggest unsafe, outdated, or hallucinated dependencies

**Endor Labs State of Dependency Management (2025):** Analysed 10,000+ GitHub repositories and tested AI coding agents across PyPI, npm, Maven, and NuGet. Only 1 in 5 dependency versions recommended by AI agents were safe to use --- the rest contained known vulnerabilities (44-49% depending on model) or were hallucinated packages. When agents were equipped with security scanning tools, safe recommendations nearly tripled from ~20% to 57%.
- Source: https://www.prnewswire.com/news-releases/endor-labs-launches-2025-state-of-dependency-management-report-finds-80-of-ai-suggested-dependencies-contain-risks-302603438.html

**USENIX Security slopsquatting research (2025):** Tested 16 models across 576,000 code samples. Roughly 20% of recommended packages did not exist. Hallucination patterns: 38% were conflations of real package names (e.g. `express-mongoose`), 13% were typo variants, 51% were pure fabrications. 43% of hallucinated names were consistently repeated across similar prompts, making them exploitable. Open-source models (CodeLlama, DeepSeek, WizardCoder) hallucinated more, but commercial models like GPT-4 still hallucinated at ~5%.
- Source: https://www.aikido.dev/blog/slopsquatting-ai-package-hallucination-attacks

**Real-world slopsquatting exploitation:** Researcher Bar Lanyado (Lasso Security) found models repeatedly suggesting `huggingface-cli` (non-existent; real install is `pip install -U "huggingface_hub[cli]"`). After uploading a benign package under that name, it received 30,000+ downloads in three months. The package `react-codeshift` (conflation of `jscodeshift` and `react-codemod`) spread to 237 repositories through forks before being claimed.
- Source: https://www.aikido.dev/blog/slopsquatting-ai-package-hallucination-attacks

### AI agents suggest outdated versions from training data

**Augment Code failure patterns (2025):** AI training data includes code from years ago. Deprecated APIs common in 2020 still appear in generated code. Example: Copilot's original model suggested `flask==1.1.4` (EOL since 2022) because it looked syntactically correct despite Flask 2.0+ introducing breaking changes. The model optimises for plausibility, not correctness.
- Source: https://www.augmentcode.com/guides/debugging-ai-generated-code-8-failure-patterns-and-fixes

**Training data cutoff problem:** Models predict tokens based on statistical patterns in frozen training data. Critical deprecations, security advisories, and ecosystem shifts after the cutoff simply do not exist in the model's worldview. This is structural, not a bug to be fixed.
- Source: https://www.augmentcode.com/guides/debugging-ai-generated-code-8-failure-patterns-and-fixes

### LLMs fail at semantic version comparison

**EdgeBit one-shot agent principles (2025):** LLMs "will reliably declare that 9.1.1 is already greater than 9.2.2." Focused tools wrapping version comparison logic are required because models cannot do semantic version arithmetic correctly. Providing just two specialised tools (version comparison and inventory lookup) anchors agent logic in ground truth and prevents hallucinated version claims.
- Source: https://edgebit.io/blog/automated-dependency-updates-with-ai/

### Lockfiles and manifests should be parsed by tools, not interpreted by LLMs

**EdgeBit (2025):** Lock files and similar metadata should be used as the source of truth. File reads take nanoseconds versus wasting tokens having the model interpret dependency files. The inventory tool reads lockfiles directly rather than having the agent parse them through tokens, making dependency state verification instantaneous and accurate.
- Source: https://edgebit.io/blog/automated-dependency-updates-with-ai/

**SafeDep SCA analysis (2025):** Diff-based approaches that try to extract updated packages from lockfiles (package-lock.json, go.mod, Cargo.lock) via LLM interpretation have inherent limitations. Parsing lockfiles or SBOMs directly is the recommended approach where reliability and accuracy are first-class requirements.
- Source: https://safedep.io/pitfalls-of-diff-based-sca-scanners/

### Context files improve agent behaviour when they specify tooling preferences

**ETH Zurich AGENTbench (2026):** Context files that restate code-inferable information decreased agent performance by ~3% and increased inference costs by 20%+. However, concrete tooling instructions ("use uv instead of pip", "use bun instead of npm") measurably improved agent compliance --- agents used the specified tool 1.6x more frequently. What helps: specific tool preferences. What hurts: architectural overviews and codebase navigation.
- Source: https://www.engineerscodex.com/agents-md-making-ai-worse
- Source: https://www.marktechpost.com/2026/02/25/new-eth-zurich-study-proves-your-ai-coding-agents-are-failing-because-your-agents-md-files-are-too-detailed/

**GitHub Copilot custom instructions:** Documentation recommends specifying library and tool preferences. Example: "Use date-fns instead of moment.js --- moment.js is deprecated and increases bundle size." Show preferred and avoided patterns with concrete code examples, as AI responds more effectively to examples than abstract rules.
- Source: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot

**Addy Osmani (2026):** Recommends CLAUDE.md and GEMINI.md files include "coding standards like style preferences, forbidden patterns, and technology choices." Notes this is "surprisingly effective to keep the model on track" and reduces AI tendency to introduce unwanted patterns. Advocates documenting constraints upfront rather than correcting AI outputs reactively.
- Source: https://addyosmani.com/blog/ai-coding-workflow/

### ADR patterns apply directly to dependency decisions

**MADR (Markdown Architectural Decision Records):** The MADR format captures considered options with pros and cons, which is directly applicable to dependency selection. The Y-statement format captures decisions as: "In the context of [use case], facing [concern] we decided for [option] to achieve [quality], accepting [downside]."
- Source: https://adr.github.io/madr/

**Lightweight ADRs:** Proposed by Thoughtworks, LADRs capture context (the problem and constraints), decision (the choice with trade-offs), and consequences. At the low level, ADRs track why a particular library was chosen for a project. One ADR commonly triggers the need for more ADRs when a big choice creates needs for smaller decisions.
- Source: https://adr.github.io/
- Source: https://www.emangini.com/blog/2022/lightweight-adr

**AWS Prescriptive Guidance:** ADRs should capture architecturally significant decisions including those affecting "important (especially external) dependencies and interfaces."
- Source: https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/adr-process.html

### Automated dependency upgrades need bounded agent behaviour

**EdgeBit principles (2025):** Successful one-shot agents for dependency updates use three principles: (1) focused tools over generic LLM reasoning for version comparison, lockfile reading, and registry queries, (2) hard failures when the agent would exit the problem's bounding box (e.g., directly mutating lockfiles with unsupported versions), and (3) bounded persistence by making certain tools unavailable to prevent agents from endlessly researching or attempting system-level changes. Result: 100% consistency across 10 test iterations.
- Source: https://edgebit.io/blog/automated-dependency-updates-with-ai/

**LADU framework for dependency upgrades (arXiv, 2025):** LLM agents achieved 71.4% precision on dependency upgrade migrations. Agents excelled at adding correct code changes but struggled with removing deprecated elements. Configuration files (YAML, deployment configs) were harder for agents than source code. Performance correlated strongly with migration documentation quality.
- Source: https://arxiv.org/html/2510.03480

### Missing context causes the most expensive failures

**Qodo State of AI Code Quality (2025):** 65% of developers say AI misses context during refactoring tasks. Cross-repository dependency changes are a specific pain point: when a shared library changed a function signature, only tools with persistent system context could detect downstream breakage.
- Source: https://www.qodo.ai/reports/state-of-ai-code-quality/

**One real-world incident from unlocked dependencies:** A company using unlocked dependencies experienced a 4.2-hour outage costing an estimated $470,000 in lost transaction fees, SLA penalties, and engineering labour.
- Source: https://www.augmentcode.com/guides/debugging-ai-generated-code-8-failure-patterns-and-fixes

## What AI Tool Makers Recommend

### Anthropic (Claude Code)

CLAUDE.md should include technology choices, forbidden patterns, and developer environment quirks. The include/exclude test: anything inferable from code, standard conventions, or config files should be excluded. Progressive disclosure ensures agents only see task-specific instructions when needed.
- Source: https://code.claude.com/docs/en/best-practices

### GitHub (Copilot)

Custom instructions (.github/copilot-instructions.md) should specify framework and library preferences. Example: "We use Bazel for managing our Java dependencies, not Maven." Use concrete code examples showing preferred vs avoided patterns. Pre-install dependencies via copilot-setup-steps.yml rather than relying on agent trial-and-error discovery.
- Source: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
- Source: https://docs.github.com/en/copilot/how-tos/agents/copilot-coding-agent/best-practices-for-using-copilot-to-work-on-tasks

### OpenAI (Codex / AGENTS.md)

AGENTS.md guides the agent on repository expectations including commands to run and project practices. Agents perform best with configured dev environments, reliable testing setups, and clear documentation.
- Source: https://developers.openai.com/codex/guides/agents-md/

### EdgeBit

Do not rely on LLMs to interpret lockfiles or compare versions. Provide focused tools for version comparison and dependency inventory. Use hard failures to prevent agents from making changes outside the defined problem space. AI augments static analysis and dependency graph knowledge; security and upgrade decisions should be based on hard facts.
- Source: https://edgebit.io/blog/automated-dependency-updates-with-ai/

## Common Failure Modes in Dependency Documentation

| Failure Mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Listing installed dependencies | Lockfiles and package manifests already express this; duplicating wastes tokens and goes stale | EdgeBit: lockfiles are source of truth; parse them, don't restate them |
| Hallucinated package suggestions | 20% of AI-recommended packages do not exist; 38% are conflations of real names | USENIX Security 2025: 576,000 samples, 16 models |
| Outdated version suggestions | Training data frozen at cutoff; deprecated packages look plausible | Augment Code: flask==1.1.4 suggested despite EOL since 2022 |
| Incorrect version comparisons | LLMs cannot reliably do semantic version arithmetic | EdgeBit: models claim 9.1.1 > 9.2.2 |
| Missing "why not" for banned packages | Agent re-introduces banned dependency because no rationale is documented | GitHub Copilot: "Use date-fns instead of moment.js" with reason |
| Missing upgrade constraints | Agent upgrades past a breaking change because constraints are undocumented | LADU: agents struggle with deprecated element removal |
| Generic "keep dependencies updated" | Not actionable; agents need specific constraints | Qodo: 65% say AI misses context; generic guidance does not help |
| Cross-ecosystem confusion | 8.7% of hallucinated Python names were valid JavaScript packages | USENIX Security 2025 |
| Restating package.json/requirements.txt | Code-inferable; decreases performance ~3%, increases cost 20% | ETH Zurich AGENTbench 2026 |

## What the Dependency Rubric Should Reward

Based on this research, mapping findings to rubric items:

| Rubric Item | Research Support |
|-------------|-----------------|
| selection_rationale (W:3) | Highest value. Why this package over alternatives, what was rejected and why. Agents cannot infer rationale from lockfiles. ADR/LADR format works well here |
| banned_alternatives (W:3) | High value. Explicit "do not use X because Y" prevents re-introduction of deprecated, vulnerable, or redundant packages. Agents re-introduce banned deps without this |
| upgrade_constraints (W:2) | Document version pins with reasons: "Pinned to React 18 until Component X migrated." Prevents agents from upgrading past breaking changes they cannot detect |
| tooling_preferences (W:2) | "Use pnpm not npm", "Use uv not pip." Concrete tool instructions measurably improve agent compliance (1.6x). Short, specific, high impact |
| cross_package_relationships (W:2) | Non-obvious dependencies between packages: "X requires Y at compatible versions", "upgrading A requires coordinated upgrade of B." Agents cannot discover implicit coupling |
| security_scanning (W:1) | How to verify dependencies: which scanners run, what to do when they flag issues. Complements rather than replaces automated tooling |
| non_obvious (W:2) | Installation gotchas, peer dependency quirks, platform-specific issues, packages that shadow each other. Only document what would cause a mistake |
| code_restatement (-2) | Essential penalty. Listing dependencies from lockfiles, restating version numbers from package manifests, reproducing README installation instructions |
| contradictions (-3) | Essential penalty. Documented versions that disagree with lockfile are worse than no docs |

## Implications for Dependency Guidance

1. **Document the "why" and "why not", never the "what."** Lockfiles, package manifests, and import statements already express what dependencies exist. Dependency docs should capture selection rationale (why this ORM, why not the popular alternative), banned packages with reasons, and constraints that prevent changes (version pins with context).

2. **Banned alternatives are as valuable as selections.** Agents hallucinate plausible package names and suggest popular-but-deprecated libraries. Explicit "do not use X; use Y instead because Z" instructions are the single most effective intervention. GitHub Copilot docs use this pattern directly.

3. **Upgrade constraints must include the reason and the exit condition.** "Pinned to v3 because v4 removed Feature X" is useful. "Pinned to v3" without rationale is nearly useless --- an agent cannot judge whether the pin is still necessary. Include when the constraint expires ("remove after migration to new API, tracked in issue #123").

4. **Tool preferences have disproportionate impact for their size.** A single line ("use pnpm not npm") measurably changes agent behaviour. These are the highest-ROI items in dependency documentation: tiny, specific, and immediately actionable.

5. **Slopsquatting is a structural risk that documentation can partially mitigate.** Listing the canonical install commands for commonly-confused packages (e.g., the correct Hugging Face CLI install) prevents agents from suggesting hallucinated alternatives. This is especially valuable for packages with non-obvious installation names.

6. **Cross-package relationships are invisible in flat dependency files.** Lockfiles list packages independently. Relationships like "these three packages must be upgraded together" or "this plugin only works with the LTS version of the framework" exist only in developer knowledge. Documenting them prevents agents from making changes that look safe in isolation but break in combination.

7. **Never let LLMs interpret lockfiles or compare versions directly.** Version arithmetic and lockfile parsing should be done by dedicated tools. Documentation should note which tools to use for these operations, not attempt to describe lockfile contents.
