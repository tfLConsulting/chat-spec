# Academic Research

Key papers relevant to chat-spec.

## ETH Zurich: Evaluating AGENTS.md (2026)

- **Paper:** [arxiv:2602.11988](https://arxiv.org/abs/2602.11988)
- **Finding:** AGENTS.md-style context files tend to *reduce* task success rates compared to no context, while increasing inference cost by 20%+
- **Developer-written files:** ~4% improvement (modest)
- **LLM-generated files:** ~3% decrease (negative effect)
- **Key insight:** Content quality matters far more than having a file at all. Style guides and testing conventions often add unnecessary complexity. Only minimal, task-relevant requirements help.
- **Implication for chat-spec:** Validates the focus on quality/maturity scoring. Bad context is worse than no context. Chat-spec's evaluation capability is arguably the most important feature.

## Codified Context: Multi-Tier Context Infrastructure (2026)

- **Paper:** [arxiv:2602.20478](https://arxiv.org/html/2602.20478)
- **Context:** Built during construction of a 108,000-line C# distributed system across 283 development sessions
- **Three-component architecture:**
  1. **Hot-memory constitution** — conventions, retrieval hooks, orchestration protocols
  2. **19 specialized domain-expert agents**
  3. **Cold-memory knowledge base** — 34 on-demand specification documents
- **Key finding:** Single-file manifests (CLAUDE.md etc.) do not scale beyond modest codebases. Multi-tier context infrastructure needed.
- **Implication for chat-spec:** Supports the `specs/` directory approach (multiple artifacts, not one file). The maturity tracking concept maps to ensuring each "cold-memory" document stays useful.

## Canterbury: Configuring Agentic AI Coding Tools (2026)

- **Paper:** [arxiv:2602.14690](https://arxiv.org/html/2602.14690v1)
- **Scope:** 2,926 GitHub repositories
- **Findings:**
  - "Distinct configuration cultures forming around different tools"
  - Claude Code users employ the broadest range of configuration mechanisms
  - Context files dominate the configuration landscape
  - Most files follow shallow hierarchical structure
- **Implication for chat-spec:** The fragmentation is real and measurable. A tool that works across configurations would have broad appeal.

## HN Discussion: Are AGENTS.md Helpful?

- **Thread:** [news.ycombinator.com/item?id=47034087](https://news.ycombinator.com/item?id=47034087)
- Community viewed 4% improvement as significant for minimal effort
- Discussion around what content actually helps vs. hurts
- General consensus: concrete commands and boundaries help, vague style guides hurt

## Sources

- [ETH Zurich paper](https://arxiv.org/abs/2602.11988)
- [Codified Context paper](https://arxiv.org/html/2602.20478)
- [Canterbury paper](https://arxiv.org/html/2602.14690v1)
- [HN discussion](https://news.ycombinator.com/item?id=47034087)
