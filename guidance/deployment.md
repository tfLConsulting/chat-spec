# Writing Deployment Specs

IaC, Dockerfiles, and CI/CD workflows express state. Deployment docs help only when they add what those files cannot.

## What to Cover

| Content | Example |
|---------|---------|
| Why environments differ | "Staging shares DB (cost); prod isolated (compliance)" |
| Manual steps outside pipelines | "DNS cutover: NS update in Cloudflare, 24h TTL wait" |
| Security invariants | "RLS on every user table; never disable auth to unblock a deploy" |
| Multi-step rollback | "Revert code, reverse migration 047, reset flag, flush cache" |
| Prescriptive monitoring | "Alert p95 > 500ms /api/checkout, page #payments-oncall" |
| Invisible constraints | "Payment gateway: 100 req/s, no retry on 429" |

## What Not to Cover

| Content | Why |
|---------|-----|
| Resource definitions from IaC | Manifests are source of truth; restating creates drift |
| Env var inventories | Discoverable from config and `.env.example` |
| "We use Docker/K8s/Terraform" | In training data; zero behaviour change |
| Pipeline step descriptions | Workflow files express this; rots fastest |
| "Deploy previous version" | Ignores migrations, flags, cache, integration state |
| "Monitor your services" | Not actionable without metrics and thresholds |

## Staleness

Deployment docs rot faster than other spec types. Emergency fixes, console changes, and tuning create invisible drift.

| Do | Don't |
|----|-------|
| Review on every infra change | "Set and forget" |
| Delete unverifiable claims | "Now deprecated" footnotes |
| Date-stamp manual procedures | Assume pipelines haven't changed |

## Self-Check

1. Does every line describe something invisible in IaC, Dockerfiles, or workflows?
2. Are security constraints hard rules, not aspirational guidelines?
3. Is rollback multi-step (code + data + flags + cache)?
4. Are monitoring thresholds specific numbers, not "monitor X"?
5. Could this be shorter without losing mistake-preventing info?

Research: `research/deployment-specs.md`.
