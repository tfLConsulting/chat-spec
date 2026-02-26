# DQ0: Principles for AI-Effective Specs

## Why this exists

This exploration establishes the foundational principles for what makes documentation genuinely useful to LLMs. Every other design question — rubrics, generation, evaluation — builds on these principles. Without them, chat-spec risks producing the kind of documentation that the ETH Zurich study found actually *hurts* AI performance.

## The research base

### The ETH Zurich warning

**Paper:** "Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?" (Gloaguen et al., ETH Zurich, February 2026, arXiv:2602.11988)

Key findings:
- LLM-generated AGENTS.md files **decreased agent performance by 2-3%** and **increased inference cost by 20%**
- The generated files primarily **duplicated existing documentation** (README, docs)
- When existing docs were removed, LLM-generated context files improved performance by 2.7% — proving they were just restating already-available information
- Developer-written files with **minimal, essential requirements not discoverable from code** tended to slightly improve performance

**What this means for chat-spec:** If we generate specs that restate what an LLM can already infer from code, package.json, directory structure, and existing READMEs, we're making things worse. Every generated spec must contain information the LLM *cannot* get from the codebase alone.

### Lost in the middle

**Paper:** "Lost in the Middle: How Language Models Use Long Contexts" (Liu et al., Stanford/Meta, TACL 2024, arXiv:2307.03172)

Key findings:
- U-shaped performance curve: LLMs use information at the **beginning and end** of context, performance degrades **>30%** for information in the middle
- Adding more context can actually hurt — GPT-3.5-Turbo performed *worse* with information buried in the middle than with no context at all

**What this means for chat-spec:** Spec documents should front-load the most important information. Don't bury key architectural decisions in the middle of a long document. Structure matters as much as content.

### Context window budgets

**Paper:** "Long Context RAG Performance of LLMs" (Databricks Mosaic Research, November 2024, arXiv:2411.03538)

Key findings:
- Most model performance decreases after 32K-64K tokens
- More documents can help retrieval up to a point, then hurt

**What this means for chat-spec:** Our entire state budget (~500-700 tokens for manifest, ~5K for all detail files) is trivial against these limits. Good. But the specs themselves (the actual artifacts) should also be compact. A 10K-token architecture doc is fine; a 50K-token one is counterproductive.

### Binary evaluation is most reliable

**Sources:**
- Arize AI (2025): Binary judgments most stable. Numeric scores "bunch, flip, collapse" with prompt wording changes
- Hamel Husain (2024-2025, hamel.dev): Across 30+ companies, domain expert pass/fail judgments correlate better with quality than granular scores
- Zheng et al. (NeurIPS 2023, arXiv:2306.05685): Strong LLM judges achieve >80% agreement with human preferences
- "Rubric Is All You Need" (ACM ICER 2025, arXiv:2503.23989): Question-specific rubrics significantly outperform generic ones

**What this means for chat-spec:** Our binary yes/no rubric design (DQ4) is directly validated. Question-specific rubrics (per artifact type) outperform generic ones — also validated.

### Context engineering

**Source:** "Effective Context Engineering for AI Agents" (Anthropic Engineering, September 2025)

Core principle: "Intelligence is not the bottleneck, context is." Good context engineering means finding the **smallest possible set of high-signal tokens** that maximize the likelihood of the desired outcome.

**Source:** Simon Willison (simonwillison.net/tags/context-engineering/): "A language model changes you from a programmer who writes lines of code to a programmer that manages the context the model has access to."

**What this means for chat-spec:** Every token in a generated spec must earn its place. The protocol isn't just about *having* documentation — it's about having the *right* documentation in the *right* format at the *right* density.

## The principles

### Principle 1: Don't tell LLMs what they already know

This is the central lesson from the ETH Zurich study. LLMs know:
- What React/Django/Express/etc. are and how they work
- Standard patterns (MVC, REST, pub-sub)
- What files like `package.json`, `Dockerfile`, `tsconfig.json` mean
- Common directory structures (`src/`, `tests/`, `lib/`)
- Language idioms and standard library usage

A spec that says "This project uses React for the frontend and Express for the backend" adds zero value — the LLM can see that from `package.json` and the import statements.

**What specs should capture instead:**
- *Why* React was chosen over alternatives (constraints the code doesn't express)
- Which React patterns this project specifically uses and which it avoids
- Non-obvious relationships ("the auth service talks to Redis, not Postgres, for session storage — this is intentional because...")
- Decisions that would be guessed wrong ("we use snake_case in this TypeScript project because the API contract requires it")
- Things that look like mistakes but aren't ("the circular dependency between user and team modules is intentional — see ADR-003")

**The test:** For every statement in a spec, ask: "Would an LLM infer this correctly from the code alone?" If yes, delete it.

### Principle 2: Structure for scanning, not reading

From the "lost in the middle" research: LLMs process documents front-to-back with a U-shaped attention curve. From Anthropic's long context research: placing documents at the top of the prompt improves response quality by up to 30%.

**Implications for spec structure:**
- **Front-load the non-obvious.** The first section of any spec should be the things an LLM would get wrong. Don't start with "Overview" boilerplate — start with "What's surprising about this project."
- **Use index/manifest patterns.** A short manifest that points to detail files lets the LLM load only what it needs. This is how aider's repo map works — tree-sitter extracts symbols, PageRank identifies the most-referenced, and a token-limited context string is built. (Source: aider.chat/docs/repomap.html)
- **Headings are navigation.** LLMs use headings to locate relevant sections. Make them specific: "API Authentication (JWT with Redis sessions)" not "Authentication."
- **Keep individual specs short.** Target 1-3KB per artifact. If a spec needs to be longer, split it or use an index-plus-detail pattern.

### Principle 3: Capture the delta, not the state

The most valuable information is the difference between what the code looks like and what someone would assume. A spec describing the current state of obvious code is redundant. A spec explaining *why the code deviates from expectations* is gold.

**Categories of high-value delta:**
- **Architectural decisions and their rationale** — the code shows *what*, the spec explains *why*
- **Gotchas and non-obvious behaviour** — things that cause bugs when assumed wrong
- **Conventions that deviate from defaults** — "we don't use the framework's ORM; here's why and what we do instead"
- **Relationships between components that aren't visible in imports** — runtime dependencies, event-driven connections, shared state through external systems
- **Historical context** — "this was migrated from X; Y remnants still exist because Z"

### Principle 4: Test with dumb agents

This is the validation strategy: if a cheap, small model with no context beyond the spec can perform correctly, the spec is working. If it can't, the spec is missing something.

**The test pattern:**

1. Take the spec in isolation (no codebase access, no other context)
2. Hand it to a cheap/fast model (Claude Haiku, GPT-4o-mini)
3. Ask it to perform a task that requires understanding the spec (e.g., "Based on this architecture doc, where would you add a new notification service?")
4. If the answer is correct, the spec contains enough signal
5. If the answer is wrong, identify what information was missing

**Why this works:**
- A weak model with good context outperforms a strong model with poor context (Anthropic's context engineering principle)
- It tests *information content*, not writing quality
- It's fast and cheap — you can run it on every spec update
- It directly simulates the real use case: an AI tool reading the spec for the first time

**Practical implementation in chat-spec:**
- The protocol could instruct the AI to run a self-check: "After generating a spec, summarise what a new AI would learn from this document that it couldn't learn from the code. If the summary is thin, the spec needs more unique information."
- For users who want rigorous validation: spin up a subagent (no project context) with only the spec and ask it to answer questions about the project. Score its answers.

This isn't formally published as a named pattern, but it's grounded in:
- ETH Zurich's methodology (testing if context files help above and beyond code)
- Anthropic's multi-agent context isolation principle ("provide each subagent only the slice of context it needs")
- Block Engineering's testing pyramid (mock providers with controlled inputs, 3x majority voting to smooth LLM judge noise)

### Principle 5: Token efficiency is a feature

Every token in a spec file costs real money and real context window capacity across every session that reads it. From Anthropic's context engineering guidance: find the "smallest possible set of high-signal tokens."

**Practical rules:**
- No preamble ("This document describes the architecture of..."). Just start with the content.
- No restating the project name, language, or framework in every file — that's in the manifest
- Use lists and tables over prose for structured information
- Prefer YAML/structured data for machine-consumed content, prose for human-consumed explanations
- One-line summaries before detailed sections (the LLM can decide if it needs the detail)

**Anti-patterns to avoid in generation:**
- "As mentioned above..." — the LLM just read it, it doesn't need a reminder
- Verbose explanations of standard patterns ("REST is a representational state transfer...")
- Defensive caveats ("This may change in the future...") — the spec tracks current state
- Section headers with no content ("## Security\n\nTBD") — either write it or don't include the section

## How these principles integrate into chat-spec

### Into rubric design (DQ4)

Add a rubric item to every artifact type:

| Item | Weight | Question |
|------|--------|----------|
| non_obvious | 2 | Contains information not inferable from code alone? |

This is arguably the most important rubric item. An artifact that scores yes on everything else but fails this one is producing the kind of documentation ETH Zurich found harmful.

### Into generation rules (DQ7 protocol)

The protocol should instruct the AI:

> When generating or improving an artifact:
> 1. First scan the codebase to understand what's already discoverable
> 2. Focus the artifact on what ISN'T discoverable: decisions, rationale, gotchas, non-obvious relationships
> 3. Do NOT restate what the code, configs, or standard conventions already express
> 4. After generating, self-check: "What does this document teach that the code doesn't?"

### Into evaluation (DQ4 rubric scoring)

When evaluating existing documentation, flag content that's purely redundant:

> "Your architecture doc spends 300 words describing the Express middleware stack, which any LLM can see from server.ts. Consider replacing this with notes on why certain middleware is ordered the way it is, or which middleware handles non-obvious edge cases."

### Into the first-run experience (DQ5)

The first artifact generated should demonstrate these principles. If the AI generates a boilerplate architecture doc that restates the obvious, the user won't see the value. The first artifact needs to surface genuinely useful, non-obvious information.

### Into testing/validation (new protocol section)

The protocol should include a validation approach:

> To validate spec quality, consider the "blank slate test": could an AI with only this spec (and no codebase access) make correct decisions about the project? If not, the spec is missing critical information. If it could make those same decisions from the code alone, the spec is adding noise rather than signal.

## Summary of principles

| # | Principle | Grounding |
|---|-----------|-----------|
| 1 | Don't tell LLMs what they already know | ETH Zurich (2026): redundant docs hurt performance |
| 2 | Structure for scanning, not reading | Lost in the Middle (TACL 2024): >30% degradation for middle content |
| 3 | Capture the delta, not the state | Anthropic context engineering: smallest set of high-signal tokens |
| 4 | Test with dumb agents | ETH Zurich methodology + Anthropic multi-agent isolation |
| 5 | Token efficiency is a feature | Databricks (2024): performance degrades after 32-64K tokens |

---

## Addendum: Phase 04 Rubric Redesign

These principles were validated and strengthened by additional research in phase 04 (see `research/ai-doc-quality.md`). Key new evidence:

- **Chroma "Context Rot" study (194k LLM calls, 18 models):** Models performed worse on coherent documents than shuffled ones. Well-written comprehensive prose paradoxically makes finding specific facts harder.
- **Instruction-following limits (arXiv:2507.11538):** At 100 instructions, accuracy drops to 50-98%. At 500, even the best model hits 62.8%. Every line of documentation competes for a finite attention budget.
- **MQM framework (translation industry):** Subtractive scoring (start at max, deduct for errors) is the proven approach for evaluating existing artifacts — directly relevant to scoring documentation that already exists vs documentation being generated fresh.

The principles here (especially Principle 1: "Don't tell LLMs what they already know") were baked into the rubric redesign by reframing rubric questions from "does X exist?" to "does X provide value beyond what code expresses?" and by adding penalty items for code restatement and contradictions.

See `specs/development-history/04-rubric-redesign/plan.md` for the full redesign.
