# AI Documentation Quality Research

Research into what makes documentation effective or harmful for AI coding tools, and how to score existing (not just new) documentation. Conducted 2026-02-26.

## Core Finding

Completeness is the wrong thing to optimise for. Documentation that restates what code expresses actively hurts AI performance. Every line competes for a finite attention budget.

## Key Evidence

### Documentation that hurts AI performance

**ETH Zurich AGENTbench (2026):** Context files that restated code-inferable information decreased AI performance by 2-3%. Restating the obvious is not neutral — it's harmful.
- Source: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study

**Chroma "Context Rot" (194k LLM calls, 18 models):** Models performed worse on coherent documents than shuffled ones. Well-written comprehensive prose paradoxically makes finding specific facts harder. Even a single distractor document reduces performance vs baseline; four compound the degradation.
- Source: https://research.trychroma.com/context-rot

**Instruction-following limits (arXiv:2507.11538, 20 models):** At 10 instructions: 90-100% accuracy. At 100: 50-98%. At 500: best model hits 62.8%. Three degradation patterns: threshold decay (reasoning models), linear decay (Claude/GPT), exponential decay (smaller models). Omission errors 30x more frequent than modification errors at high density.
- Source: https://arxiv.org/html/2507.11538v1

**Context discipline (arXiv:2601.11564):** Accuracy degradation was mild (98.5% to 98%), but latency degradation was severe: 719% spike at max context. Context discipline is infrastructure-critical.
- Source: https://arxiv.org/html/2601.11564v1

### Lost in the Middle

**Liu et al. (TACL 2024):** Performance highest when relevant info is at the beginning or end of context. Significantly degrades when in the middle of long contexts. This is architectural (RoPE positional encoding), not a prompt engineering failure.
- Source: https://arxiv.org/abs/2307.03172

### RAG and retrieval quality

**ICLR 2025:** Models can perform worse with full retrieval sets than with no documents at all. Semantically similar but incorrect passages ("distractors") are far more damaging than random irrelevant text.
- Source: https://proceedings.iclr.cc/paper_files/paper/2025/file/5df5b1f121c915d8bdd00db6aac20827-Paper-Conference.pdf

### Context engineering

**Anthropic:** Goal is "the smallest set of high-signal tokens that maximize the likelihood of your desired outcome." Four categories of harmful context: poisoning (incorrect info), distraction (irrelevant), confusion (similar-but-different), clash (contradictory).
- Source: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

**JetBrains Research (2025):** Agent-generated context rapidly deteriorates into noise. Observation masking (hiding older details) increased solve rates by 2.6% while reducing costs by 52%.
- Source: https://blog.jetbrains.com/research/2025/12/efficient-context-management/

## What AI Tool Makers Recommend

### Anthropic (Claude Code)
- CLAUDE.md should contain: commands Claude can't guess, style rules that differ from defaults, testing instructions, architectural decisions, dev environment quirks, gotchas
- CLAUDE.md should NOT contain: anything inferable from code, standard conventions, what config files say, directory structures, "write clean code"
- Self-test: "Would removing this cause Claude to make mistakes?" If not, cut it
- ~150-200 instructions is the practical ceiling for consistent following
- Source: https://code.claude.com/docs/en/best-practices

### Cursor
- Individual rule files under 500 lines
- Focused, composable rules over monolithic files
- Reference files with @filename rather than inlining

### GitHub Copilot
- "Short, self-contained statements that add context"
- 1,000 lines maximum per instruction file — quality deteriorates beyond this
- Short imperative directives over narrative paragraphs
- Source: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot

### Windsurf
- Bullet points and lists over prose
- NEVER/ALWAYS flags for critical rules
- No generic rules ("write good code") — already in training

## Common Failure Modes in Existing Documentation

### Volume failures
| Mode | Mechanism |
|------|-----------|
| Instruction drowning | Rules lost in long files; AI follows some, ignores others |
| Context saturation | Large docs fill context, less room for actual code |
| Primacy bias | Later instructions deprioritized vs earlier ones |
| Omission at scale | At high counts, AI skips requirements entirely |

### Content quality failures
| Mode | Mechanism |
|------|-----------|
| Stale docs (doc drift) | Code changes but docs don't; AI builds on false premises |
| Contradiction paralysis | Conflicting instructions force arbitrary choice |
| Code redundancy | Restating code wastes tokens, adds confusion |
| Context poisoning | Incorrect information corrupts downstream reasoning |
| Semantic near-misses | Topically related but wrong docs are worse than irrelevant ones |

### Structure failures
| Mode | Mechanism |
|------|-----------|
| Missing hierarchy | Skipped heading levels break LLM's cognitive model |
| Prose over structure | Long paragraphs harder to parse than lists/tables |
| Preamble pollution | "This document describes..." wastes tokens |
| Terminology inconsistency | Different words for same concept forces guessing |

## Scoring Framework Research

### Additive vs subtractive

Most documentation scoring (including chat-spec's current rubrics) is **additive** — start at 0, gain points for presence. This rewards comprehensive docs even if they're verbose, stale, or contradictory.

**MQM (Multidimensional Quality Metrics)** from translation is the proven **subtractive** alternative — start at max, deduct for errors. Severity levels: Neutral (0x), Minor (1x), Major (5x), Critical (25x). A single critical error tanks the score.
- Source: https://themqm.org/error-types-2/the-mqm-scoring-models/

### Diataxis framework

Distinguishes **functional quality** (accuracy, completeness, consistency — measurable) from **deep quality** (pleasant to use, anticipates needs — subjective). Explicitly states: "Documentation can be accurate, complete, consistent and also useless."
- Source: https://diataxis.fr/quality/

### Google's documentation philosophy

"Dead docs are bad. They misinform, they slow down, they incite despair." "A small set of fresh and accurate docs is better than a large assembly of documentation in various states of disrepair." Default to deletion when docs might be wrong. The "bonsai tree" metaphor.
- Source: https://google.github.io/styleguide/docguide/best_practices.html

### What predicts AI helpfulness (ranked by impact)

1. **Relevance** — only task-relevant docs reach context
2. **Freshness/accuracy** — matches current codebase state
3. **Information density** — ratio of actionable content to prose
4. **Internal consistency** — no contradictions
5. **Structural clarity** — headings, code blocks, explicit declarations
6. **Conciseness** — same info in fewer tokens

## Implications for chat-spec Rubrics

Current rubrics have zero negative signals. They cannot detect the problems that actually exist in real documentation:

| Problem | Prevalence | Current coverage |
|---------|-----------|-----------------|
| Restates code | Very common | `non_obvious` (W:2) hints at it |
| Stale / contradicts code | Very common | `current` (W:1) — weakest item |
| Verbose, low info density | Very common | Nothing |
| Internal contradictions | Common after edits | Nothing |
| Aspirational (intent not reality) | Common | Nothing |
| Empty/TBD sections | Common | Protocol mentions, not scored |

The `current` item is weight 1 in every rubric. Research says accuracy/freshness is the second most impactful quality signal. This is a severe misweighting.
