# The README is the tool

## The insight

The purest version of "chat-spec is a protocol" is: **the GitHub README is the entire product.** A developer, in whatever AI tool they're already using, says:

> "Go read https://github.com/lindow-consulting/chat-spec and do what it says for this project"

The AI fetches the README, understands the methodology, and runs it. No install. No CLI. No MCP server. No custom command. Just a URL and an instruction.

This is zero-friction taken to its actual limit.

## What the README needs to contain

For this to work, the README has to be simultaneously:

1. **A human-readable explanation** — what chat-spec is, why you'd use it
2. **An AI-executable specification** — precise enough that an AI can follow it as a procedure
3. **The manifest format spec** — so the AI knows how to read/write the manifest
4. **The default rubrics** — so the AI has scoring criteria
5. **The artifact catalogue** — so the AI knows what to look for

That's a lot. But it's not impossible — it just means the README is structured with both audiences in mind. Humans read the top and skip the details. AIs read the whole thing.

Or: the README is the pitch + quickstart, and it links to a `PROTOCOL.md` (or similar) that contains the full machine-readable spec. The user says "go read the chat-spec repo" and the AI reads both.

## What this changes about the design

### Distribution is a URL

No package manager. No install step. No version pinning. The repo IS the product. When chat-spec improves, people get the improvement next time they (or their AI) read the URL.

This is both a strength (zero friction, always current) and a risk (breaking changes affect everyone immediately, no version control for consumers). Could mitigate with tagged releases / versioned URLs.

### The AI is the runtime

Every AI tool that can fetch a URL and read files becomes a chat-spec runtime. Claude Code, Cursor, Copilot, ChatGPT with browsing, Gemini — all of them. No integration work required.

The quality of the experience depends on the AI's capability. Claude Code (which can read/write files, fetch URLs, follow complex instructions) will give a great experience. A less capable tool might stumble. That's OK — graceful degradation is fine.

### The protocol must be robust to interpretation variance

Different AIs will interpret the same instructions slightly differently. The protocol needs to be:
- **Specific** where it matters (manifest format, rubric structure, scoring mechanism)
- **Flexible** where variance is acceptable (conversation style, how it explains findings)
- **Self-validating** where possible (the manifest schema is checkable; the rubric answers are yes/no)

### Versioning becomes interesting

If the README is the tool, and it evolves, you need:
- **Manifest schema version** — so an AI reading v2 of the protocol can still handle a manifest created with v1
- **Rubric versioning** — so scores remain comparable when rubrics change
- **Protocol version** — clearly stated so the AI knows what version it's following

The manifest already has `schema_version: 1`. That's the right instinct.

### The "quick bootstrap" use case is perfect for this

From DP7, the secondary user is "developer encountering a new codebase." Their workflow is:

1. Clone a repo
2. Open their AI tool
3. Say "go read chat-spec's docs and set up specs for this project"
4. AI does it

No prior relationship with chat-spec required. No installation. The URL is the entire adoption path.

### But what about repeat usage?

First run: user pastes the URL, AI reads the protocol, runs it. Great.

Second run: user says... what? "Run chat-spec again"? The AI doesn't remember the URL from last session.

Options:
- **The manifest contains the protocol URL.** When chat-spec creates a manifest, it includes a reference to the protocol version it used. Next session, the AI reads the manifest, sees the URL, fetches the protocol, and continues.
- **A local CLAUDE.md (or equivalent) is created on first run.** This small file says "this project uses chat-spec" and includes the protocol reference. Now the AI knows automatically.
- **The user just knows.** They say "run chat-spec" and the AI, seeing the specs/ directory and manifest, understands what to do. The protocol is implicit in the artifacts.

The second option is elegant: chat-spec's first action is to write a thin CLAUDE.md that includes something like:

```
## Chat-spec
This project uses chat-spec (https://github.com/lindow-consulting/chat-spec).
Read the protocol at the link above, then read specs/manifest.yaml to continue.
```

Now every future AI session in this project automatically knows about chat-spec. The URL propagates itself.

## The README structure (sketch)

```
# chat-spec

One sentence: what it is.
One paragraph: why you'd use it.

## Quick start

> Paste this in your AI assistant:
> "Read https://github.com/lindow-consulting/chat-spec and run it on this project"

That's it. That's the install.

## What happens

[Human-readable explanation of the examine/evaluate/propose loop]

## The protocol

[Link to PROTOCOL.md — the full, AI-readable specification]

## The manifest

[Brief explanation + link to format spec]

## Default rubrics

[Brief explanation + link to rubric definitions]
```

The PROTOCOL.md is the real product. The README is the landing page.

## What about the MCP/agent path?

The "README is the tool" approach doesn't preclude packaging chat-spec as an MCP server or custom command later. It's the other direction:

1. **V0:** The README/protocol is the tool. Zero install. Works today.
2. **V1:** Package the protocol as an MCP server for a more structured, reliable experience. The protocol is still the spec; the MCP server is a convenience wrapper.
3. **V2+:** CLI, CI integration, etc. if there's demand.

The protocol-first approach means the MCP server is just "a thing that reads the protocol and follows it with better tooling." It's an optimisation, not a different product.

## Open questions

- How long can the protocol doc be before AI tools struggle to follow it? (Context window limits)
- Should the protocol be one file or split into sections? (One file is simpler to fetch; split is easier to maintain)
- How do you handle AI tools that can't fetch URLs? (Fallback: copy-paste the protocol, or have a local copy)
- Does this approach create a dependency on GitHub uptime? (Manifest should contain enough to continue without fetching the protocol each time)
- Is "read this URL and do what it says" a security concern? (The AI will ask for permission before writing files, so probably fine — but worth noting)
