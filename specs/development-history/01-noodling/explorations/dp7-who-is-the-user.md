# DP7: Who is the user?

## The candidates

### A. Solo developer using AI assistants

**Profile:** Individual developer (freelancer, indie hacker, or IC at a company) who uses Claude Code, Cursor, Copilot, etc. daily. Has noticed that AI output quality varies wildly depending on project context. Wants to systematically improve their AI's effectiveness.

**What they care about:**
- Zero friction — no setup ceremony, should work in minutes
- Immediate value — first run should produce something useful
- Low maintenance — shouldn't become another chore
- Works with their tool — probably Claude Code or Cursor, not some separate thing

**What they don't care about:**
- Team governance, consistency across developers
- CI integration (maybe — some do, most don't for personal projects)
- Comparative scoring against other projects
- Enterprise features

**What chat-spec looks like for them:**
A command they run occasionally. "/chat-spec" in Claude Code. It looks at their project, suggests improvements to their AI context, does the work if they say yes. They run it when they notice their AI being dumb, or every few weeks as maintenance.

**Adoption path:** Find it on GitHub/Twitter. Try it in 5 minutes. Keep using it if the first run is impressive. Abandon it if it feels like work.

### B. Tech lead / engineering manager

**Profile:** Responsible for a team's productivity and code quality. Has adopted AI tooling across the team and wants consistent, high-quality AI context so everyone's AI assistant does good work. Worried about the "works on my machine" problem but for AI context.

**What they care about:**
- Consistency — same AI context quality across all team members
- Measurability — "is our AI context improving?" needs a number
- Governance — who changes the specs? Review process?
- CI integration — quality gates on docs, like they have on code
- Onboarding — new team members (and new AI tools) get up to speed fast

**What they don't care about:**
- Doing the work themselves (want the team to do it, or AI to do it)
- Zero-install (they can mandate tooling)
- Works with every AI tool (they've probably standardised on one or two)

**What chat-spec looks like for them:**
A CI check that scores the project's AI-readiness. A dashboard showing trends. A process where someone on the team runs chat-spec periodically and the output gets reviewed in a PR. Maybe integrated into sprint retrospectives: "our AI context score dropped 5 points this sprint."

**Adoption path:** Hear about it at a conference or from a peer. Evaluate it formally. Adopt it as a team practice. Need documentation, support, maybe a paid tier.

### C. The AI assistant itself

**Profile:** Not a human. Chat-spec is invoked automatically by AI coding tools before they start work on a project. It's infrastructure, not a user-facing tool.

**What it cares about:**
- Machine-readable output — structured data, not prose
- Fast — shouldn't add minutes to every AI interaction
- Reliable — deterministic enough to be automated
- Minimal — only the context the AI actually needs

**What it doesn't care about:**
- Pretty output, conversational interaction, human-readable reports
- Scoring for motivation (AIs don't get motivated)
- Gradual improvement narrative (just give me the best context now)

**What chat-spec looks like for it:**
An MCP resource or tool that AI assistants call to get structured project context. The manifest is the API. Scoring is used to prioritise what context to include (high-maturity docs first, skip low-quality ones). The AI-as-user doesn't run chat-spec to improve docs — it runs it to consume them.

**Adoption path:** Built into AI tool integrations. MCP server that tools discover automatically. The human never interacts with chat-spec directly.

## The insight I keep circling

These three users want fundamentally different things:

| | Solo dev | Tech lead | AI-as-user |
|---|---------|-----------|------------|
| Primary value | Better AI output | Team consistency | Structured context |
| Interaction model | Conversational | Process/CI | Programmatic |
| Frequency | Occasional | Scheduled | Every session |
| Generation needed? | Yes, heavily | Yes, governed | No, consumption only |
| Form factor | Command in AI tool | CLI + CI | MCP server/resource |

**You can't optimise for all three at once.** The solo dev wants chat-spec to be invisible and helpful. The tech lead wants it to be visible and measurable. The AI-as-user wants it to be structured and fast.

## But maybe the phasing helps

What if you design for one user first and the others follow naturally?

### Path 1: Solo dev first

Build for the solo dev. Make it a command in Claude Code (or MCP). Make first-run impressive. Make it conversational and helpful.

Then notice: the specs and manifest it produces are just files. A tech lead can wrap CI around those files. An AI-as-user can read those files. The core artifacts serve everyone — only the interaction layer differs.

**Risk:** Solo dev tooling might be too casual for team adoption. No governance, no process, no dashboards.

### Path 2: Tech lead first

Build for the team. CLI with CI integration, scoring dashboards, governance features.

**Risk:** Over-engineered for individuals. High setup cost kills casual adoption. You build enterprise software before you have enterprise customers.

### Path 3: AI-as-user first

Build the manifest format and make it an MCP resource. Let AI tools consume it.

**Risk:** No human ever interacts with it. No one has a reason to create or maintain the specs. You've built a format that nothing fills.

### The obvious answer

**Solo dev first. The artifacts serve everyone. The interaction layer differentiates later.**

The solo dev is the person who will actually create and maintain the specs. Without them, there's nothing for tech leads to govern or AIs to consume. Start where the content gets created.

Then:
- CI integration is just "score the manifest in CI" — a thin wrapper, not a separate product
- AI-as-user is just "read the manifest as an MCP resource" — almost free
- Team features are governance on top of the existing artifacts — not a rebuild

## A secondary persona worth considering

### D. Developer evaluating a new-to-them codebase

Not maintaining their own project, but trying to understand someone else's. They clone a repo, want to work with AI on it, and the project has no AI context.

**What they care about:**
- Quick bootstrap — "I just cloned this, make it AI-ready enough to work with"
- Don't need perfection — good-enough context for a focused task
- Temporary — might not stick around to maintain this

This is interesting because it's a different lifecycle:
- No iterative improvement — one-shot is fine
- Speed matters more than quality
- The output might be disposable

This could be a distinct mode: **`chat-spec quick`** — generate rough specs fast, don't worry about rubrics and scoring, just get enough context for the AI to be useful.

It's also the easiest way to demonstrate value. "Clone any repo, run chat-spec quick, your AI instantly works better." That's a compelling demo.

---

## Where I'm landing

**Primary user: solo developer using AI assistants.** Design everything for them first.

**Secondary user: developer encountering a new codebase.** This is the demo use case and a distinct mode.

**Tertiary users (design for later, but don't block):**
- Tech leads (CI integration, team features)
- AI-as-user (MCP resource for programmatic access)

**Design implication:** The interaction should be conversational, low-friction, and immediately valuable. The artifacts should be structured enough for programmatic use later. Don't build governance or dashboards yet.

---

## Sub-questions

- Is the "quick bootstrap" mode different enough from the main flow to be a separate command, or just the first run?
- Should chat-spec work on projects you don't own? (i.e., specs in a fork/branch, not committed to main)
- How do you handle the transition from solo to team? (When a solo dev's project becomes a team project)
