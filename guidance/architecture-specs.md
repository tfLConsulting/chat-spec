# Writing Architecture Specs for AI Tools

Architecture docs help agents only when they contain what code cannot express. Component listings and overviews are ignored or harmful.

## What to Document

| Content | Example |
|---------|---------|
| Decision rationale | "Redis over in-process cache — workers don't share memory" |
| Rejected alternatives | "Rejected event sourcing; query complexity for reporting" |
| Non-obvious boundaries | "Auth owns token validation; gateway forwards headers" |
| Cross-cutting constraints | "Writes via command bus — no direct repo calls" |
| Hidden dependencies | "Billing webhook completes before order reads payment" |

Use Context/Decision/Consequences format.

## What to Leave Out

| Content | Why |
|---------|-----|
| Component/directory listings | Agents discover structure from code |
| "Uses clean architecture" | In training data; no behaviour change |
| Import/dependency graphs | Package manifests express this |
| Monolithic overview | Scoped subsystem docs outperform overviews |
| Aspirational design | Agent builds on fiction, not reality |

## Structure

Scope to subsystems — one doc per service. Front-load constraints.

| Do | Don't |
|----|-------|
| `docs/arch/payments.md` | `ARCHITECTURE.md` covering everything |
| Constraints first | Inventory first, decisions buried |
| Delete when decisions reverse | "Now deprecated" footnotes |

## Staleness

Wrong architecture docs are the most damaging failure mode. Can't verify a claim against current code? Delete it. Missing beats wrong.

## Self-Check

1. Does every entry describe something not discoverable from code?
2. Are decisions in Context/Decision/Consequences format?
3. Is each doc scoped to one subsystem?
4. Is every claim true of the codebase *right now*?
5. Are constraints at the top, not buried after descriptions?

Research: `research/architecture-specs.md`.
