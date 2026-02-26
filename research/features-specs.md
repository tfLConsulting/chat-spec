# Features Documentation for AI-Assisted Development

Research into what makes features/capabilities documentation effective for AI coding tools, and common failure modes in feature specs. Conducted 2026-02-26.

See also: `ai-doc-quality.md` for underlying research on documentation quality, context rot, and instruction-following limits.

## Core Finding

Feature documentation should describe behaviour and intent that cannot be discovered from exploring the UI or reading the code. AI agents build codebase understanding through direct exploration (reading code, running tools, examining test output), so feature docs that restate what exploration reveals waste context budget and can actively degrade performance. The documentation that helps most captures *why* features exist, *how* they interact in non-obvious ways, and *what status* they have in the project lifecycle.

## Key Evidence

### Agents discover features through exploration, not documentation

AI coding agents build capability awareness through direct code reading, iterative feedback loops, test-driven validation, and execution results. MIT's Missing Semester (2026) notes that agents primarily understand software through conversation history and provided context, with performance degrading as context grows "even if it doesn't overflow the context window." Feature documentation competes with code-level context for the same finite budget.
- Source: https://missing.csail.mit.edu/2026/agentic-coding/

The AGENTbench study (ETH Zurich, 2026) found that agents "largely ignored high-level architecture overviews meant to guide file navigation." Feature lists and codebase overviews are discoverable and add noise rather than value.
- Source: https://arxiv.org/html/2602.11988v1
- Summary: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study

### Domain facts outperform behavioural instructions

The Codified Context study (arXiv:2602.20478) found that over 50% of effective specification content was domain facts (project-specific knowledge, formulas, code patterns, known failure modes) rather than behavioural instructions. For complex domains like networking, domain knowledge reached 65% of specification content. These specifications were "written for AI consumption (explicit code patterns with file paths, parameter names, and expected behavior)" rather than human-oriented prose.

The study structured feature documentation using:
- **Core mechanics tables** with precise parameters
- **Correctness pillars** enumerating non-negotiable requirements
- **Failure mode matrices** mapping symptoms to causes to fixes
- **Decision trees** for routing between modes

This is the strongest evidence for what the features rubric calls `feature_detail` and `non_obvious`: domain facts that explain *why* and *how*, not *what*.
- Source: https://arxiv.org/html/2602.20478

### Documentation quality is a measurable multiplier

DORA's 2025 research confirms that "AI-accessible internal data" is a statistically significant multiplier for individual effectiveness and code quality. But it draws a critical distinction: "feeding large, raw documents into an AI's context window is often counterproductive and frequently results in hallucinations." Effective documentation provides "only the most specific context for the current request."
- Source: https://dora.dev/capabilities/ai-accessible-internal-data/

### Stale feature documentation creates compounding errors

Russell Moss (2025) describes documentation drift as the "silent killer of AI-assisted development." When documented features don't match reality, agents build on false premises, and each AI-assisted commit drifts further from the actual codebase. The Codified Context study identified "specification staleness as the primary failure mode," citing an instance where outdated combat documentation caused agents to wire calculations through deprecated stat fields.
- Source: https://dev.to/mossrussell/your-ai-agent-is-coding-against-fiction-how-i-fixed-doc-drift-with-a-pre-commit-hook-1acn
- Source: https://arxiv.org/html/2602.20478

### AI tools struggle with deprecated features and status tracking

LLMs don't reason about feature deprecation unless explicitly told. An AI may generate code using deprecated APIs (e.g., `urllib2` in Python 3) because it doesn't check the user's runtime version or current status. When a context window contains stale tool outputs or deprecated state, agents "fixate on past patterns rather than the immediate instruction."
- Source: https://venturebeat.com/ai/why-ai-coding-agents-arent-production-ready-brittle-context-windows-broken

This makes explicit feature status tracking (the `feature_status` rubric item) more important than it might appear. Without it, agents default to training data which may include deprecated patterns.

## What Helps: Feature Documentation Patterns

### Describe behaviour and intent, not UI

Anthropic's context engineering guidance says effective documentation provides "the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome." For features, this means documenting *why* a feature exists and *how* it behaves in non-obvious cases, not *what buttons it has* or *what the code does*.
- Source: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

### Scope boundaries prevent agent drift

GitHub's analysis of 2,500+ agent files found that agents "will happily add features not asked for and introduce new dependencies without warning" without explicit boundaries. A three-tier boundary system works: always do / ask first / never do. Documenting what the project *doesn't* do is as important as what it does.
- Source: https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/
- Source: https://coder.com/blog/launch-dec-2025-agent-boundaries

### Feature interactions need explicit documentation

Traditional AI tools process files individually and cannot understand implicit dependencies between features. The Codified Context study addressed this through multi-subsystem specifications that synthesise related domains (e.g., a networking agent embedding coordination knowledge spanning 4-5 dependent subsystems) and cross-reference linking between documentation tiers.

Augment Code found that "changes to one service trigger awareness in agents working on dependent services, preventing breaking integrations because someone changed a response format without updating consumers."
- Source: https://arxiv.org/html/2602.20478
- Source: https://www.augmentcode.com/guides/spec-driven-ai-code-generation-with-multi-agent-systems

### User workflows should focus on non-obvious paths

Spotify's Honk system (1,500+ merged PRs) found that feature documentation is most effective when it defines "the desired end state, ideally in the form of tests" and clearly states "when not to take action." Documenting every happy-path workflow is unnecessary; agents discover those through exploration. Document the edge cases, the non-obvious paths, and the surprising expected outcomes.
- Source: https://engineering.atspotify.com/2025/11/context-engineering-background-coding-agents-part-2

### Concrete examples over abstract descriptions

Addy Osmani (2025), synthesising evidence from 2,500+ agent files, found that specs for AI agents should focus on "what and why, more than the nitty-gritty how." A good feature spec uses concrete examples ("user can add/edit tasks; data persists; app is responsive") rather than abstract descriptions. One real code snippet showing expected behaviour beats three paragraphs describing it.

Two anti-patterns in feature specs: (1) overly generic descriptions that expect agents to infer intent, and (2) exhaustive case-by-case documentation that breaks when encountering unexpected scenarios.
- Source: https://addyosmani.com/blog/good-spec/
- Source: https://www.oreilly.com/radar/how-to-write-a-good-spec-for-ai-agents/

## What Hurts: Feature Documentation Failure Modes

| Failure mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Listing obvious functionality | Restates what UI or code exploration reveals; wastes context budget | AGENTbench: -2-3% performance with generated docs that duplicated existing info |
| Restating UI or code | Competes for attention with the actual code the agent needs to read | Chroma context rot: models performed worse on coherent documents than shuffled ones |
| Missing non-obvious behaviour | Agent must discover design rationale by trial and error, if at all | Codified Context: over 50% of effective spec content was domain facts |
| No feature status | Agent uses deprecated patterns from training data; generates code against retired APIs | VentureBeat: stale state causes agents to "fixate on past patterns" |
| Aspirational features documented as real | Agent implements features that don't exist; "coding against fiction" | Moss (2025): each AI commit drifts further from reality |
| Feature interactions undocumented | Agent breaks cross-cutting concerns when changing one feature | Augment Code: implicit dependencies invisible without explicit documentation |
| Exhaustive case-by-case documentation | Brittle; breaks when encountering scenarios not covered | Spotify Honk: exhaustive docs fail on unexpected cases |
| No scope boundaries | Agent adds unrequested features, introduces new dependencies | GitHub 2,500-repo study: agents drift without explicit boundaries |

## Implications for the Features Rubric

### Well-weighted items

- `core_features` (W:3) and `feature_detail` (W:3): The Codified Context study validates that domain facts and behaviour documentation are the most valuable content type, justifying the highest weight.
- `non_obvious` (W:2): Every source reviewed confirms that non-discoverable information is the only documentation that reliably helps. This could arguably be W:3.
- `feature_status` (W:2): Evidence from deprecated API struggles and documentation drift research supports this weight. Without explicit status, agents default to training data.

### Items deserving attention

- `scope` (W:1): Evidence from GitHub's 2,500-repo analysis shows scope boundaries are critical for preventing agent drift. Weight 1 may undervalue this.
- `interactions` (W:1): Feature interactions represent exactly the kind of non-obvious, cross-cutting information agents cannot discover independently. The Codified Context study suggests this deserves more weight.
- `current` (W:2): The "coding against fiction" problem makes accuracy the single most dangerous failure mode. Research from earlier `ai-doc-quality.md` work suggests this should be the highest-weighted item, not a mid-tier one.

### Penalty validation

- `code_restatement` (-2): Strongly validated. AGENTbench, Chroma context rot, and Anthropic's guidance all confirm that restating discoverable information hurts performance.
- `contradictions` (-3): The heaviest penalty is justified. Stale feature documentation causes compounding errors as agents build on false premises.
