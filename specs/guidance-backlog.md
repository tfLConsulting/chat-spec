# Guidance Backlog

Future guidance files for the `guidance/` directory. Each follows the recipe at `specs/guidance-recipe.md`.

## Existing

| File | Covers |
|------|--------|
| `ai-doc-quality.md` | Foundational — what helps vs. hurts AI tools |
| `architecture-specs.md` | Writing architecture docs |
| `claude-md.md` | CLAUDE.md-specific patterns |
| `conventions.md` | Writing conventions/patterns docs |
| `dev-context.md` | Dev-context files (CLAUDE.md, AGENTS.md, cursorrules) |
| `features.md` | Writing feature specs |

All four core artifact types now have guidance. Remaining tiers are specialized and architecture-specific.

## Tier 2 — Common Specialized Artifacts

Most projects with a backend need at least two of these.

| File | Covers | Why |
|------|--------|-----|
| `api-docs.md` | API endpoint documentation | Highest code-restatement risk — OpenAPI/Swagger already exists |
| `data-model.md` | Entity/schema documentation | Schema files are self-documenting; guidance on what goes beyond ERDs |
| `deployment.md` | Deploy/infra documentation | Mix of IaC-readable config and tribal knowledge |
| `security.md` | Auth/authz/threat model docs | Either dangerously stale or uselessly generic without guidance |

## Tier 3 — Less Common Specialized Artifacts

Project-dependent. Build when a project actually tracks the artifact type.

| File | Covers | Why |
|------|--------|-----|
| `dependencies.md` | Dependency choice documentation | `package.json` says what; guidance on documenting *why* without bloat |
| `design-system.md` | UI component/visual standards | Storybook and CSS variables express a lot; what's left? |
| `performance.md` | SLOs, bottlenecks, optimization | Numbers go stale fast; guidance on what ages well |
| `integration.md` | Third-party service documentation | Client code is readable; guidance on documenting the contract |

## Tier 4 — Architecture-Specific Guidance

Helps write better specs when a project uses these patterns. Not tied to a single artifact type — applies across architecture, conventions, deployment, etc.

| File | Covers | Why |
|------|--------|-----|
| `serverless-patterns.md` | Serverless/FaaS architecture (Cloud Functions, Lambda) | What to document: cold start constraints, function isolation boundaries, event-driven flows, trigger relationships. What's already in config: function definitions, memory/timeout settings, trigger bindings |
| `nextjs-specs.md` | Next.js application patterns | What to document: rendering strategy decisions (SSR vs SSG vs ISR per route), middleware chains, data fetching patterns, caching decisions. What framework docs cover: file-based routing, API routes, built-in components |
| `monorepo-docs.md` | Documentation in monorepos | Where to scope docs (root vs package vs app level), progressive loading across workspaces, avoiding duplication, shared library documentation patterns |
| `firebase-patterns.md` | Firebase ecosystem documentation | What to document: security rules rationale, Firestore data modelling decisions, function architecture, environment separation patterns, emulator setup gotchas. What's config-readable: firebase.json, rules files, index definitions |
| `ci-cd-docs.md` | CI/CD pipeline documentation | What to document: environment promotion logic, manual gates, deployment prerequisites, post-deploy verification. What workflow files express: build steps, trigger conditions, artifact paths |
| `openapi-codegen.md` | Generated API client documentation | What to document: custom middleware, error handling wrapping, retry/auth injection. What the spec already covers: endpoints, request/response schemas, status codes |
| `ai-features.md` | AI/ML feature documentation | What to document: model selection rationale, prompt management strategy, fallback chains, cost/latency trade-offs, evaluation approach. What code expresses: prompt templates, API calls, response parsing |
| `react-component-patterns.md` | React + design system documentation | What to document: composition rules, state management boundaries, component API decisions, accessibility requirements beyond ARIA defaults. What Storybook/code covers: prop interfaces, visual examples, basic usage |
| `document-generation.md` | PDF/Word/email template documentation | What to document: template data contracts, rendering pipeline decisions, format-specific constraints, binary output handling. What code expresses: template structure, generation logic |

## Tier 5 — Cross-Cutting Topics

Applies across all artifact types and architectures.

| File | Covers | Why |
|------|--------|-----|
| `spec-maintenance.md` | Keeping docs fresh, when to delete vs update | Staleness is the #1 doc failure mode; no maintenance guidance exists |
| `multi-tool-context.md` | Writing context for Claude + Cursor + Copilot + Windsurf | Each tool loads context differently; portable patterns |
| `diagram-specs.md` | When/how to use diagrams (Mermaid, ASCII) in specs | Diagrams can help or hurt depending on tool support |

---

## Prioritisation Notes

- **Tier 1 complete** — all four core artifact types have guidance.
- **Next up: Tier 2** — pick based on which specialized artifacts your project tracks.
- **Tier 4 picks** — choose based on your project stack. A Firebase + Next.js monorepo would prioritise: `monorepo-docs.md`, `firebase-patterns.md`, `nextjs-specs.md`, `serverless-patterns.md`.
- **One guide per session** — the recipe produces better output with focused research.
- **Each guide is ~2KB** — expect 30-45 min per guide including research.
