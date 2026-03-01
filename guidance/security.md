# Writing Security Specs

Auth middleware and security packages are in code. Security docs help only when they add what code and config cannot express.

## What to Cover

| Content | Example |
|---------|---------|
| Permission model + rationale | "Members: own org only; billing-admin: child orgs (reseller)" |
| Trust boundaries | "Browser → gateway (JWT) → internal (trusts gateway, no re-auth)" |
| Auth decisions that look wrong | "No refresh tokens on mobile — compliance" |
| Invariants, no single enforcement point | "Own-org only — RLS + middleware + queue consumers" |
| Known agent failure areas | "Sanitise before logging; no stack traces in errors" |
| Non-default config rationale | "CSP unsafe-inline: legacy analytics — remove post-migration" |
| Secret management flow | "Vault at deploy; never in env/logs/responses" |
| Cross-service security contracts | "Trusts payment JWT for amount; re-validate if expanding" |

## What Not to Cover

| Content | Why |
|---------|-----|
| Helmet/CORS/passport config | In code; restating drifts and costs tokens |
| "Use HTTPS," "validate input" | Universal knowledge; no behaviour change |
| OWASP Top 10 checklist | Already in training data; no project specificity |
| Auth library API docs | Agent reads the library |
| STRIDE/DREAD without specific threats | Unfalsifiable |

## Staleness

Security docs rot fastest. Stale permissions are worse than none.

| Do | Don't |
|----|-------|
| Review on every auth/permission change | "Set and forget" |
| Delete unverifiable claims | Mark "deprecated" and leave |

## Self-Check

1. Does every line describe something invisible in code, config, or middleware?
2. Are trust boundaries explicit — location, what crosses, what gets validated?
3. Would an agent change its behaviour on a security task after reading this?
4. Are invariants specific rules, not aspirational guidelines?
5. Could this be shorter without losing actionable info?

Research: `research/security-specs.md`
