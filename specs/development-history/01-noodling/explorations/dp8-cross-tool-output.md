# DP8: Is cross-tool output a first-class feature?

## The question

Should chat-spec generate CLAUDE.md, .cursorrules, AGENTS.md, etc. from the specs? Or are the specs themselves the output?

## The market context

From the research:
- 45% of AI-configured repos use CLAUDE.md
- 40.6% use AGENTS.md
- Many use multiple formats
- 8+ "config sync" tools already exist (Ruler, ContextPilot, rulesync, etc.)
- The fragmentation is real and annoying

## Three positions

### A. Chat-spec generates tool-specific configs

Specs are the source of truth. Chat-spec synthesises them into CLAUDE.md, .cursorrules, AGENTS.md, etc. as a build step.

**For:** Solves a real pain point. "Write specs once, get configs for every AI tool." Strong value prop.

**Against:** Now chat-spec is a config sync tool. It needs to track every AI tool's format, keep up with changes, handle format-specific features. This is a maintenance sinkhole. And 8+ tools already do this — it's a crowded, commodity space.

### B. Specs ARE the context (no generation needed)

The specs/ directory is what AI tools should read. CLAUDE.md just says "read specs/ for project context." No generation step.

**For:** Simpler. No format tracking. Specs are the canonical, tool-agnostic form.

**Against:** Not how AI tools work today. CLAUDE.md has specific conventions. .cursorrules has its own format. AI tools don't know to look in specs/ without being told. You still need something in each tool's config pointing to the specs.

### C. Chat-spec generates a thin pointer, not full configs

Chat-spec writes a minimal CLAUDE.md (or equivalent) that says: "This project uses chat-spec. Read specs/manifest.yaml for the artifact index, then read the relevant specs based on what you're working on."

The tool-specific file is a shim, not a copy of all the specs. It's 5-10 lines, not a comprehensive document.

**For:** Tiny maintenance surface. Works with existing tool conventions. Specs remain canonical.

**Against:** Relies on AI tools being smart enough to follow the pointer. Today, they mostly are. But older/simpler tools might not handle multi-file context well.

## The nuance: what do CLAUDE.md et al actually contain?

Looking at what these files typically include:
1. **Project description** — what this is
2. **Conventions** — coding style, patterns to follow
3. **Constraints** — things to avoid, limitations
4. **File structure** — where things live
5. **Tool-specific instructions** — "use bun not npm", "run tests with X"

Items 1-4 map directly to chat-spec artifacts. Item 5 is operational and probably belongs in the tool-specific config regardless.

So the question becomes: should CLAUDE.md contain a copy of the architecture spec, or should it say "read specs/architecture.md"?

If AI tools can follow references (and modern ones can), the pointer approach works. If they can't, you need to inline the content.

## A pragmatic answer

**For V1: Option C (thin pointer) + a build command for Option A.**

Default behaviour: chat-spec writes a minimal CLAUDE.md that references the specs directory. This works for Claude Code and Cursor (which can read files).

Optional command: `chat-spec export --format claude-md` generates a full CLAUDE.md by inlining specs content. For users who want it, or for tools that can't follow references.

This means:
- Chat-spec isn't a config sync tool by default
- Cross-tool support exists but isn't the core value prop
- The export command can expand to new formats without complicating the core

---

## Landing

**Thin pointer by default. Full export as optional command.** Don't compete in the config sync space. Let the specs be the source of truth and write minimal shims to point AI tools at them.

**But:** This is a V1 decision. If users consistently ask for full CLAUDE.md generation, it moves to default. Let usage decide.
