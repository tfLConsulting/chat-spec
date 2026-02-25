# DP1: What is chat-spec, physically?

## The options

### A. A CLI tool (npm/pip/cargo install)

You install it. You run it. It reads your project, writes files, exits.

**For:**
- Familiar distribution model — people know how to install and run CLIs
- Can be deterministic where needed (parsing, diffing, file watching)
- Can have a test suite, CI, versioning — it's "real software"
- Can do things AI can't: fast file scanning, git operations, structured output
- Works offline

**Against:**
- Another thing to install and maintain — updates, breaking changes, dependency hell
- Competes with a crowded space (Repomix, Spec Kit, etc. are all CLIs)
- The interesting part (evaluation, generation) requires AI anyway, so the CLI is mostly a shell around API calls
- Language choice creates friction: Node? Python? Rust? Each alienates someone

**The honest question:** What would the CLI actually do that isn't an AI call? File scanning, git diffing, manifest parsing. Is that enough to justify a standalone tool?

### B. A prompt/protocol (no install)

Chat-spec is a specification — a directory convention, a manifest format, and a set of prompts. Any AI assistant can follow the protocol. You distribute it as documentation, or as a CLAUDE.md/AGENTS.md file that teaches the AI the methodology.

**For:**
- Zero friction — nothing to install, works immediately
- Tool-agnostic by nature — works wherever there's an AI
- The "tool" is just files in your repo, versioned with your code
- Can't break, can't have dependency issues, can't go out of date (the prompts are the tool)
- Distribution is trivial: copy a file, or reference a URL

**Against:**
- Relies entirely on AI capability — if the AI misunderstands the protocol, there's no fallback
- No determinism — each run might interpret the protocol slightly differently
- Hard to test, hard to guarantee consistency
- "It's just prompts" might not feel like a product — harder to charge for, harder to build a community around
- Can't do things like git integration, CI hooks, or dashboards without additional tooling

**The honest question:** Is "a well-structured prompt" enough, or does that undervalue what chat-spec is? Prompt engineering is real, but it's also fragile.

### C. An AI agent (MCP server / custom command)

Chat-spec is an agent that runs inside your AI assistant. It has a system prompt, access to tools (filesystem, git), and a structured methodology. You invoke it like a command within your existing workflow.

**For:**
- Runs where the user already is — no context switching
- Has AI capability baked in — the evaluation and generation are native
- Can be conversational — ask questions, clarify, iterate
- MCP is becoming a standard — distribute once, works in multiple hosts
- Feels like a natural extension of your AI assistant, not a separate tool

**Against:**
- Coupled to AI assistant ecosystems — what if MCP doesn't win? What if the host changes its API?
- "A custom command" doesn't feel substantial — is this a product or a prompt?
- Context window limitations are real — the agent can only hold so much
- Harder to test and version than a CLI
- The agent's behaviour is shaped by the host model — Claude vs GPT vs Gemini may produce very different results

**The honest question:** Is this just Option B with extra steps? Or does the structured agent bring enough over a prompt-protocol to matter?

### D. Hybrid: thin CLI + AI core

A minimal CLI that handles the deterministic parts (file scanning, manifest parsing, git integration, CI hooks) and delegates to AI for the intelligent parts (evaluation, generation, prioritisation). The CLI is the orchestrator; the AI is the brain.

**For:**
- Best of both worlds — deterministic where it matters, intelligent where it counts
- The CLI can work offline for basic operations (show manifest, check structure)
- CI integration is natural
- Can support multiple AI backends (Claude, GPT, local models)
- Feels like a "real tool" while leveraging AI capability

**Against:**
- Most complex to build and maintain
- Still has the install/dependency problem
- API key management adds friction
- Two things to get right instead of one

---

## The dimension I keep coming back to

The interesting thing about chat-spec is that **the valuable part is the methodology, not the mechanism**. The insight — score your AI context quality, track it over time, improve iteratively — doesn't inherently require any particular form factor.

Which makes me think the form factor decision should be driven by:

1. **What's the minimum viable surface area?** What's the simplest thing that delivers the core value?
2. **Where does the user already spend their time?** Making them context-switch to a new tool is a cost.
3. **What's the distribution story?** How do people find it, try it, adopt it?

### Applying those lenses:

**Minimum viable:** Option B (prompt/protocol). You could ship chat-spec tomorrow as a well-crafted CLAUDE.md file and a manifest format spec.

**Where users are:** Option C (agent). Developers are increasingly in AI-assisted environments. An MCP server or Claude Code command meets them there.

**Distribution:** This is where it gets interesting. Option B distributes virally (copy a file). Option A distributes via package managers (familiar but competitive). Option C distributes via MCP registries (new and growing). Option D is the hardest to distribute.

### A possible path

What if the answer is **B first, then C, with D as a distant maybe?**

1. **V0.1:** Chat-spec is a protocol — a manifest format spec, a directory convention, and a reference CLAUDE.md that implements the methodology. Ship this to validate the idea.
2. **V0.5:** Package the protocol as an MCP server or Claude Code custom command. Same methodology, but structured and reusable. This is what most people interact with.
3. **V1+:** If there's demand for CI integration, offline operation, or multi-backend support, build the thin CLI.

This path has the advantage of forcing the methodology to be good first, before adding tooling complexity. If the protocol doesn't work well as "just prompts," no amount of CLI scaffolding will fix it.

---

## Open sub-questions

- If it's a protocol, how do you version it? What happens when the manifest format changes?
- If it's an MCP server, which operations are tools vs. resources vs. prompts in MCP terms?
- How do you test a protocol? (Maybe: a reference implementation + a test project)
- Does the form factor affect pricing/business model? (Protocol is hard to monetise. Tool is easier.)
