# Writing Feature Specs

Feature docs help only when they capture what exploration cannot reveal.

## What to Cover

| Content | Example |
|---------|---------|
| Why the feature exists | "Backoff + jitter to avoid thundering herd on payment gateway" |
| Non-obvious behaviour | "Soft-delete cascades to `order_items` not `invoices`" |
| Feature status | "Batch export: **deprecated** — use streaming export (v2.3+)" |
| Scope boundaries | "Search indexes title + body. Never: file contents, comments, PDFs" |
| Feature interactions | "Cart recalculates on coupon apply AND shipping change" |
| Opaque domain rules | "Net-30: `due_date = issued_date + 30 calendar days`, not business days" |

Over 50% of effective spec content is domain facts — lead with those.

## What Not to Cover

| Content | Why |
|---------|-----|
| Feature lists / inventories | Discoverable from code and tests |
| UI descriptions | Visible from the app or templates |
| Happy-path workflows | Agent traces these from code |
| Implementation details matching code | Duplication hurts (-2-3%) |
| Aspirational features | Agent builds on fiction; errors compound |
| Exhaustive case-by-case docs | Brittle; fail on uncovered scenarios |

## Status Tracking

Without explicit status, agents default to training data and use deprecated APIs.

| Status | Meaning |
|--------|---------|
| `deprecated` | Still works; name the replacement |
| `removed` | Gone; name what replaced it |
| `experimental` | May change without notice |

Mark status at the top of each feature section. Omit `active` — it's the default.

## Self-Check

1. Does every line describe something undiscoverable from code?
2. Are scope boundaries explicit — does AND does not?
3. Are cross-feature interactions documented where changes break each other?
4. Is every feature's status accurate *right now*?
5. Could this be shorter without losing mistake-preventing info?

Research: `research/features-specs.md`.
