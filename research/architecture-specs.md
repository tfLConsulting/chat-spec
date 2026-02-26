# Architecture Specification Research

Research into what makes architecture documentation effective for AI coding tools, and what content helps vs. hurts agent performance. Conducted 2026-02-26.

## Core Finding

Architecture documentation helps AI agents only when it contains information the agent cannot discover from code. Architectural overviews, directory structures, and component listings that restate code structure are ignored by agents or actively degrade performance. The content that works is narrow: non-obvious boundaries, cross-cutting constraints, decision rationale, and hidden dependencies.

## Key Evidence

### Architecture overviews are ignored

**ETH Zurich AGENTbench (2026):** Across 138 instances from 12 repositories, LLM-generated context files decreased agent performance by 2-3%. High-level "here is how the codebase is structured" sections were largely ignored by agents during code navigation. Directory structure listings (e.g., `src/components/`, `src/hooks/`) were flagged as "restating the obvious." Developer-written files improved performance by only ~4%, and only when they contained non-discoverable information like backward-compatible migration requirements or deprecated code paths.
- Source: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study
- Source: https://arxiv.org/html/2602.11988v1

**The Decoder summary of the same study:** LLM-generated context files were "largely redundant with documentation already present in the repositories." Context files work best when teaching genuinely missing knowledge — framework versions not in training data, non-standard build systems, or infrastructure constraints invisible in source.
- Source: https://the-decoder.com/context-files-for-coding-agents-often-dont-help-and-may-even-hurt-performance/

### What architecture content actually helps agents

**Codified Context paper (arXiv:2602.20478, 2026):** Documents a three-tier architecture documentation system used across 283 development sessions on a 108k-line C# codebase. Tier 1 (hot memory): ~660-line constitution with routing rules and conventions, always loaded. Tier 2: 19 domain-expert agent specs (~9,300 lines) embedding project-specific knowledge. Tier 3 (cold memory): 34 on-demand specification documents retrieved via MCP.

Key findings: Agents with substantial pre-loaded domain context performed significantly better — the networking agent specification (915 lines, ~65% domain knowledge) caught determinism issues that general sessions missed. Failure-mode documentation ("symptom-cause-fix" tables) prevented recurring errors across sessions. But stale specifications actively misled agents — legacy stat fields caused agents to wire calculations through deprecated paths.

57% of agent invocations used project-specific specialists. 80% of human prompts were under 100 words, suggesting pre-loaded context reduced explanation needs.
- Source: https://arxiv.org/html/2602.20478v1

**vFunction (2025-2026):** Without architectural context, AI agents produce code that lacks separation of concerns, skips resilience patterns, omits observability, and creates tight coupling. Agents "fill gaps arbitrarily, often with simplistic decisions optimised for immediate functionality." Agents need: layered structure expectations, non-functional requirements, system design principles, and runtime context about existing business logic and integration constraints.
- Source: https://vfunction.com/blog/vibe-coding-architecture-ai-agents/

### Architecture Decision Records are high-value, low-cost

**Chris Swan (2025):** ADRs are effective for AI assistants because they combine "enough structure to ensure key points are addressed, but in natural language, which is perfect for LLMs." The Context/Decision/Consequences format captures exactly what AI agents need: why a choice was made, what alternatives were rejected, and what constraints apply. ADRs are increasingly seen as standard practice for AI-assisted development rather than an elite-team technique.
- Source: https://blog.thestateofme.com/2025/07/10/using-architecture-decision-records-adrs-with-ai-coding-assistants/

### Spec-driven development validates architecture-first

**GitHub Spec Kit (2025):** Introduced a structured process: constitution (non-negotiable principles), specifications (what to build), and technical plans (architecture, stack, constraints). The planning stage "maps the specification to actual system architecture by defining which services, modules, data flows, state logic, and observability hooks will be affected," preventing agents from "inventing new structures or guessing integration patterns."
- Source: https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

**Martin Fowler (2025):** Despite detailed specifications, AI agents still "generated features not requested, changed assumptions mid-stream, and claimed success when builds failed." Fowler concluded that SDD works best for large feature builds, not small fixes — "using a sledgehammer to crack a nut."
- Source: https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

**Red Hat (2025):** With architecture specs, developers reported "95% or higher accuracy in implementing specs on the first go." Key: specs should be "scoped tightly to avoid overlap" and kept modular. Layered detail: functional specs (what), language-agnostic design (how), then language-specific rules.
- Source: https://developers.redhat.com/articles/2025/10/22/how-spec-driven-development-improves-ai-coding-quality

### Right abstraction level

**Addy Osmani (2025-2026):** Recommends specs focus on "what and why, more than the nitty-gritty how." Effective architecture context includes: executable commands, boundaries (what agents should never touch), constraints, and integration points — not exhaustive technical diagrams. "One real code snippet showing your style beats three paragraphs describing it."
- Source: https://addyosmani.com/blog/good-spec/
- Source: https://addyosmani.com/blog/ai-coding-workflow/

**Cursor token tax (2026):** Every word in context rules is a token cost. The "Rule of Three": don't codify a rule the first time an AI makes a mistake — only add it if the AI fails the same architectural pattern three times. This filters for genuinely non-obvious constraints rather than one-off corrections.
- Source: https://medium.com/@peakvance/guide-to-cursor-rules-engineering-context-speed-and-the-token-tax-16c0560a686a

### Positional effects on architecture docs

**Liu et al. (TACL 2024):** Performance degrades by 30%+ when relevant information is in the middle of context rather than start or end. This is architectural (RoPE positional encoding), creating a U-shaped attention curve. Implication for architecture docs: put the most critical constraints and non-obvious decisions at the top, not buried after component listings.
- Source: https://arxiv.org/abs/2307.03172

## Common Failure Modes in Architecture Documentation

| Failure Mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Component/directory listings | Agents discover structure from code; listings waste tokens | AGENTbench: agents "largely ignore" structural overviews |
| Import/dependency restating | Package manifests already express this; duplication adds noise | AGENTbench: LLM-generated files "redundant with existing docs" |
| Stale architecture claims | Agents build on false premises, wire through deprecated paths | Codified Context: legacy fields caused incorrect wiring |
| Comprehensive prose overviews | Long coherent text makes finding specific facts harder | Chroma Context Rot: shuffled fragments outperform coherent docs |
| Generic design principles | "Use clean architecture" is in training data already | vFunction: only project-specific constraints add value |
| Architecture buried mid-document | Lost-in-the-middle effect reduces attention to ~70% | Liu et al.: 30%+ degradation for mid-context information |

## What the Architecture Rubric Should Reward

Based on this research, the architecture rubric items align well with what actually helps. Mapping research findings to rubric items:

| Rubric Item | Research Support |
|-------------|-----------------|
| components (W:3) | Valuable only when documenting roles/interactions NOT obvious from code structure. AGENTbench shows component listings are ignored |
| relationships (W:3) | High value — implicit dependencies and communication patterns are genuinely non-discoverable |
| data_flow (W:2) | Valuable for cross-boundary flows. Agents cannot infer multi-service data paths |
| non_obvious (W:2) | Core of what works. AGENTbench: only "non-discoverable information" improved performance |
| boundaries (W:2) | High value — ownership, responsibility boundaries, what not to touch |
| entry_points (W:1) | Moderate value — non-obvious entry points (background jobs, event handlers) are genuinely hidden |
| persistence (W:1) | Valuable when explaining why data lives in one store vs. another |
| external (W:1) | Valuable — hidden service dependencies and constraints |
| constraints (W:1) | High value relative to weight — trade-offs and constraints are exactly what ADRs capture |
| current (W:2) | Critical — stale docs actively mislead agents (Codified Context paper) |
| code_restatement (-2) | Essential penalty — AGENTbench proves restatement hurts by 2-3% |
| contradictions (-3) | Essential penalty — Codified Context shows stale fields cause incorrect wiring |

## Implications for Architecture Guidance

1. **Document decisions, not structure.** ADR-style content (Context/Decision/Consequences) is the highest-value format. Component listings and directory trees are actively wasteful.

2. **Front-load constraints and boundaries.** Due to positional encoding effects, put non-obvious constraints, ownership boundaries, and critical architectural decisions at the top of architecture specs.

3. **Scope documents to subsystems.** The Codified Context paper's success came from 34 scoped specification documents, not one monolithic architecture overview. Smaller, focused docs are retrieved and consumed more reliably.

4. **Architecture docs are load-bearing infrastructure.** Stale architecture documentation is worse than none. A wrong boundary claim or deprecated component reference causes agents to build on false premises.

5. **The right abstraction is "why and what constraints," not "what exists."** Agents can discover what exists from code. They cannot discover why something was built that way, what alternatives were rejected, or what constraints apply.

6. **Code restatement in architecture docs is measurably harmful.** The AGENTbench study directly quantifies this: context files restating code-inferable information decreased performance by 2-3%. This validates the rubric's code_restatement penalty item.
