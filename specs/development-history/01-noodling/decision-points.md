# Decision Points

Extracted from the five idea sketches. These are the assumptions that recur or diverge across ideas — the things we've been taking positions on without properly deciding.

---

## DP1: What is it, physically?

**The question:** Is chat-spec a CLI tool, a prompt/protocol, an AI agent, or something else?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | CLI tool |
| 2 — The Conversation | Prompt/protocol (no install) |
| 3 — Quality Gate | CLI tool (linter-shaped) |
| 4 — Bootstrap Loop | AI agent (custom command / MCP) |
| 5 — Spec Registry | CLI tool + cloud service |

**Why this matters:** This is the most fundamental design decision. It determines distribution, dependencies, the UX model, and what's technically possible.

**Tension:** Zero-install (Idea 2) maximises adoption but limits capability. A CLI tool (Ideas 1, 3) is conventional but means maintaining a tool. An agent (Idea 4) is the most capable but couples to a host environment.

**Not yet explored.**

---

## DP2: Does it generate or only evaluate?

**The question:** Should chat-spec write/improve spec documents, or only score them and tell you what's wrong?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Both (evaluate then generate with consent) |
| 2 — The Conversation | Both (AI does the work conversationally) |
| 3 — Quality Gate | Evaluate only — generation is someone else's job |
| 4 — Bootstrap Loop | Both, with generation as the primary mode |
| 5 — Spec Registry | Both, informed by templates |

**Why this matters:** If it only evaluates, it's simpler and avoids the ETH Zurich problem (AI-generated docs can hurt). If it generates, it's more useful but takes on quality risk.

**Tension:** The research says quality >>> existence. AI-generated context that's mediocre might be worse than no context. But asking users to write docs manually is a big ask — they won't do it.

**Not yet explored.**

---

## DP3: How does scoring work?

**The question:** Rubric-based per-artifact scoring (1-5)? Dimension-based holistic scoring (0-100)? Something else?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Per-artifact, 1-5, rubric-defined |
| 2 — The Conversation | Per-artifact, 1-5, rubric in manifest |
| 3 — Quality Gate | Holistic dimensions, 0-100, weighted |
| 4 — Bootstrap Loop | Per-artifact, 1-5, with history |
| 5 — Spec Registry | Per-artifact with baselines for comparison |

**Why this matters:** Scoring is the core differentiator. If it's too granular, it's noisy. If it's too abstract, it's not actionable. If it's not reproducible across runs, trend tracking is meaningless.

**Tension:** AI scoring against rubrics is inherently fuzzy. A 1-5 scale is coarse enough to be reproducible but maybe too coarse to show progress. A 0-100 scale is more expressive but probably not reproducible (the AI will give different scores each time for the same document).

**Not yet explored.**

---

## DP4: What's the manifest format?

**The question:** YAML, JSON, Markdown, or something else?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | YAML |
| 2 — The Conversation | JSON |
| 3 — Quality Gate | YAML |
| 4 — Bootstrap Loop | YAML |
| 5 — Spec Registry | (implied YAML) |

**Why this matters:** The manifest needs to be readable by humans, writable by AI, and parseable by tools (if we build any).

**Tension:** YAML is more human-readable. JSON is more universally parseable by AI (they're trained on more JSON). Markdown would be most natural for AI to read/write but hardest to parse programmatically. TOML is another option.

**Probably a minor decision. Lean towards whatever the host environment favours.**

---

## DP5: What artifacts does it track?

**The question:** Is there a fixed catalogue of artifact types, or is it fully customisable?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Built-in catalogue, extensible |
| 2 — The Conversation | Implied standard set |
| 3 — Quality Gate | Configurable dimensions |
| 4 — Bootstrap Loop | Agent proposes based on project |
| 5 — Spec Registry | Registry-defined per project type |

**Why this matters:** Too rigid and it doesn't fit real projects. Too flexible and there's no shared vocabulary.

**Tension:** The original idea lists specific artifacts (features, architecture, design system, constraints, standards, dev history). But a CLI tool doesn't need design system docs, and a design system doesn't need API docs. The artifact set should adapt to the project.

**Not yet explored.**

---

## DP6: How does it handle change detection?

**The question:** Does chat-spec need to know what changed since the last run?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Git diff since last run |
| 2 — The Conversation | No change detection, judges fresh each time |
| 3 — Quality Gate | Git integration for "commits since last doc update" |
| 4 — Bootstrap Loop | Manifest timestamps + observation |
| 5 — Spec Registry | (not specified) |

**Why this matters:** Change detection enables "your docs are stale because code changed" — a key feature. But it adds git coupling.

**Tension:** Some environments don't use git. And even with git, "code changed" doesn't mean "docs are wrong" — you'd need to understand what changed. AI-based evaluation (Idea 2) sidesteps this by just judging the docs directly, but loses the "staleness" signal.

**Not yet explored.**

---

## DP7: Who's the user?

**The question:** Individual developer? Team lead? DevOps? The AI assistant itself?

Not explicitly addressed in any idea, but the answer shapes everything.

**Possible users:**
- **Solo developer** using AI assistants — wants their AI to be more effective
- **Tech lead** wanting consistent AI context across a team
- **The AI itself** — chat-spec as something AI tools invoke automatically before starting work

**Why this matters:** Solo dev wants zero friction. Team lead wants governance. AI-as-user wants machine-readable everything.

**Not yet explored.**

---

## DP8: Is cross-tool output a first-class feature?

**The question:** Should chat-spec generate CLAUDE.md, .cursorrules, AGENTS.md etc., or just maintain its own specs/?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Yes, derivative files from specs |
| 2 — The Conversation | No, the specs are tool-agnostic |
| 3 — Quality Gate | Checks for it as a "context accessibility" dimension |
| 4 — Bootstrap Loop | Not a priority, specs are universal |
| 5 — Spec Registry | (not specified) |

**Why this matters:** The research shows real fragmentation (45% CLAUDE.md, 40% AGENTS.md). If chat-spec can be the "one source" that generates tool-specific configs, that's a strong value prop.

**Tension:** Generating tool-specific configs means tracking their formats, which change. It also makes chat-spec a "config sync tool" — a crowded space (8+ tools already do this). But it could be the differentiator that makes people actually adopt chat-spec.

**Not yet explored.**

---

## DP9: Is a community/registry layer needed at V1?

**The question:** Should chat-spec have shared rubrics, templates, and baselines, or is local-only sufficient?

| Idea | Position |
|------|----------|
| 5 — Spec Registry | Core feature |
| Others | Doesn't come up |

**Why this matters:** A registry creates network effects and solves "who defines quality." But it's a massive scope increase.

**Strong lean: V2 at earliest. Local-only for V1.**

---

## DP10: How prescriptive should it be?

**The question:** Does chat-spec have opinions about what your specs should contain, or is it a blank framework?

| Idea | Position |
|------|----------|
| 1 — Living Manifest | Opinionated catalogue with override |
| 3 — Quality Gate | Opinionated defaults with customisation |
| 4 — Bootstrap Loop | Agent decides based on project |
| 2 — The Conversation | Light touch — protocol, not opinions |

**Why this matters:** Opinionated tools are easier to start with but harder to adopt in unusual projects. Flexible frameworks are harder to start but fit everywhere.

**Tension:** The research shows that generic/boilerplate context hurts (ETH Zurich). But having no starting point means most users never start. The right answer is probably: opinionated defaults that are easy to customise, with clear "this is a default, override if needed" signalling.

**Not yet explored.**

---

## Priority ranking (gut feel, to be validated)

1. **DP1** — What is it? (everything follows from this)
2. **DP2** — Generate or evaluate? (defines the value proposition)
3. **DP7** — Who's it for? (shapes all UX decisions)
4. **DP3** — How does scoring work? (the core differentiator)
5. **DP10** — How prescriptive? (adoption vs. quality trade-off)
6. **DP5** — What artifacts? (practical but not philosophical)
7. **DP8** — Cross-tool output? (nice-to-have vs. core)
8. **DP6** — Change detection? (implementation detail)
9. **DP4** — Manifest format? (minor)
10. **DP9** — Registry? (V2)
