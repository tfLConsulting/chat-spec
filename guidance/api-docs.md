# Writing API Documentation for AI Agents

Agents select endpoints via semantic matching on descriptions. Poor descriptions cause wrong-endpoint selection; 50%+ of failures on unfamiliar APIs are fabricated endpoints.

## What to Document

| Content | Example |
|---------|---------|
| Disambiguating descriptions | "Paginated invoices by status/date" not "Gets data" |
| Scopes/roles per endpoint | "`billing:read`; org-admin sees all, member sees own" |
| Implicit validation rules | "Currency must match account's base currency" |
| Side effects | "Creating subscription generates prorated invoice" |
| Enum values with consequences | "`void` triggers refund webhook" not `string` |
| Rate limits with backoff | "100 req/min; 429 includes `Retry-After`" |
| Pagination deviations | "Cursor-based; cursor expires after 5 min" |
| Request/response examples | 1-3 per endpoint: minimal to full payloads |

Examples are highest-leverage — models learn patterns from examples more reliably than schema.

## What Not to Document

| Content | Why |
|---------|-----|
| Schema field types/structure | Parseable from OpenAPI; restating wastes tokens |
| Standard HTTP semantics | Training data; no behaviour change |
| Full API surface in one doc | 30+ tools degrade selection accuracy; group by domain |
| Auth as UI flows | Agent can't "click authorize"; document token exchanges |

## Auth for Agents

Human-oriented auth docs fail agents. Document:

- Token exchange as HTTP requests
- Which token represents user vs. agent (identity propagation)
- Required `aud` claim and scope binding per endpoint

## Self-Check

1. Does every description disambiguate its endpoint from similar ones?
2. Are business rules beyond schema validation documented?
3. Does every endpoint have at least one request/response example?
4. Are scopes specified per endpoint, not just "requires auth"?
5. Would removing any line cause an agent to pick the wrong endpoint?

Research: `research/api-docs-specs.md`.
