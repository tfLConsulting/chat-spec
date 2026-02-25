# DP10: How prescriptive should chat-spec be?

## The spectrum

**Fully prescriptive:** "Every project must have these 8 artifacts. Here are the rubrics. Here's the format."

**Fully flexible:** "Define whatever artifacts you want. Write your own rubrics. Chat-spec is just a scoring engine."

## Why the answer matters more than it seems

This isn't just a UX preference. It determines:
- **First-run experience:** Prescriptive = immediate value. Flexible = "now what?"
- **Quality of output:** Prescriptive rubrics capture collective knowledge about what good docs look like. Custom rubrics might be garbage.
- **Community/ecosystem:** Prescriptive creates a shared vocabulary. Flexible means everyone speaks their own language.
- **Maintenance burden:** Prescriptive means chat-spec maintainers curate rubrics. Flexible means users are on their own.

## The lessons from the research

**ESLint model works.** ESLint ships with recommended rules but lets you override everything. Most people use the defaults. Power users customise heavily. Nobody complains about the prescriptive defaults because they're clearly labelled as defaults.

**AGENTS.md finding is relevant.** The ETH Zurich paper found that generic project instructions barely help. The implication: generic rubrics might score things that don't matter and miss things that do.

**Spec Kit criticism is instructive.** People called it "reinvented waterfall" partly because it prescribed a rigid 4-phase process. Prescription that feels heavy-handed triggers resistance.

## A framework: what should be prescribed?

### Always prescribed (the protocol layer)

These are structural decisions that need to be consistent for the ecosystem to work:

- **Manifest format:** Where it lives, what fields it has, the schema
- **Directory convention:** specs/ directory, well-known file locations
- **Scoring mechanism:** Checklist-based, yes/no questions, weighted (from DP3)
- **Maturity levels:** 1-5 scale mapped from checklist scores

If these vary per project, cross-project tooling (CI integration, MCP resources, team dashboards) becomes impossible. This is like HTTP headers — the format is prescribed, the content is flexible.

### Prescribed by default, customisable (the content layer)

These ship with chat-spec but can be overridden:

- **Artifact catalogue:** The list of artifact types chat-spec knows about
- **Default rubrics:** Checklist items for each built-in artifact type
- **Priority heuristics:** "Architecture matters more than design system for a CLI tool"

This is the ESLint model. "Here's what we think a good architecture rubric looks like. It works for most projects. Change it if you need to."

### Never prescribed (the project layer)

These must come from the project:

- **Which artifacts matter:** Not every project needs every artifact type
- **Custom artifacts:** Project-specific things that don't fit built-in types
- **Rubric overrides:** "For this project, API documentation is more important than architecture"
- **Scoring weights:** How much each artifact contributes to the overall score

## The artifact catalogue question

What artifact types should ship as defaults?

Working from the original idea + research findings, here's a proposed catalogue:

### Core (most projects need these)

1. **Architecture** — system structure, components, relationships, data flow
2. **Features** — what the project does, discrete capabilities
3. **Conventions** — coding standards, patterns, naming, testing expectations
4. **Development context** — history, decisions made, lessons learned

### Common (many projects need these)

5. **API documentation** — endpoints, schemas, authentication
6. **Data model** — entities, relationships, storage
7. **Deployment** — how to deploy, environments, infrastructure
8. **Dependencies** — key dependencies, why they were chosen, version constraints

### Specialised (some projects need these)

9. **Design system** — visual standards, component library, accessibility
10. **Security model** — authentication, authorization, threat model
11. **Performance** — SLOs, known bottlenecks, optimisation constraints
12. **Integration** — third-party services, APIs consumed, webhooks

### Meta (chat-spec's own artifacts)

13. **AI context configuration** — CLAUDE.md, .cursorrules, etc. (how the project configures AI tools today)

The key: chat-spec should propose which of these matter for a given project based on what it sees. "This is a CLI tool, so I'm suggesting Architecture, Features, Conventions, and Development Context. I'm skipping Design System and API Documentation because they don't seem relevant. Sound right?"

## How prescriptive about artifact content?

Beyond "you should have an architecture doc," should chat-spec prescribe what's in it?

**Option A: Template-based.** Provide a template for each artifact type. "An architecture doc should have: Overview, Components, Relationships, Data Flow, Deployment, Decisions."

**Option B: Rubric-driven.** The rubric implicitly defines the content. If the rubric asks "Does it describe deployment?" then you know the doc should include deployment.

**Option C: Example-based.** Show examples of good artifacts rather than templates.

**Lean: Option B.** The rubric is the prescription. If you satisfy all rubric items, the content is right. Templates are too rigid ("fill in the blanks" leads to generic output — the ETH Zurich problem). Examples are useful for inspiration but aren't actionable.

This means the rubric does double duty:
1. It scores existing artifacts
2. It guides the creation of new ones ("to create an architecture doc that scores 4/5, you need to answer these 8 questions")

That's elegant. The rubric isn't separate from the content guidance — it IS the content guidance.

## The first-run prescriptiveness

First run is where prescriptiveness matters most. The user has nothing. Chat-spec needs to:

1. Scan the project
2. Propose which artifacts matter (from the catalogue)
3. Propose rubrics for each (from defaults, adjusted for project type)
4. Start building

This is inherently prescriptive — and that's fine. The user can say "no, I don't need deployment docs" and the manifest adjusts. But the starting point should be opinionated enough to be useful without interrogating the user for 20 minutes.

**The heuristic:** Be prescriptive on first run, be descriptive thereafter. Start by telling the user what they probably need. Then shift to showing them what they have and where the gaps are.

---

## Where I'm landing

**Three layers of prescriptiveness:**
1. **Protocol (always prescribed):** Manifest format, directory convention, scoring mechanism
2. **Content defaults (prescribed, overridable):** Artifact catalogue, default rubrics, priority heuristics
3. **Project configuration (never prescribed):** Which artifacts matter, custom rubrics, weights

**Rubrics as the universal prescription mechanism.** They score AND guide. No separate templates needed.

**Opinionated first run, descriptive steady-state.** Chat-spec proposes a starting manifest based on project analysis. User adjusts. Then it shifts to evaluation mode.

---

## Sub-questions

- How do you present the default catalogue without overwhelming the user? (Maybe: show the recommended 4-5, mention the full catalogue exists)
- Should rubrics be versioned? (When chat-spec updates default rubrics, what happens to existing scores?)
- How do project-type heuristics work? ("This looks like a React SPA" — based on what signals?)
