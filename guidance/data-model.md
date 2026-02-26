# Writing Data Model Specs

Agents read schemas, ORM models, and migrations. Document only what those cannot express.

## What to Cover

| Content | Example |
|---------|---------|
| Business terms | "`active user` = `WHERE last_login > 30d AND status = 'active'`" |
| Implicit relationships | "Order completion publishes to `billing` queue — no FK" |
| Denormalisation rationale | "`user_count` on `teams`: truth is `memberships`, sync by `TeamCounterJob`" |
| State machines | "`draft → confirmed → shipped`. `cancelled` only from `draft`/`confirmed`" |
| Soft-delete / scoping | "All models except `audit_logs` use `deleted_at`" |
| Storage tier decisions | "Sessions in Redis (ephemeral). Audit logs in Postgres (compliance)" |
| Polymorphic patterns | "`commentable_type`/`commentable_id` — `Post`, `Ticket`, `Invoice`" |
| Validation beyond schema | "Email unique via `LOWER(email)` — case-insensitive business rule" |

## What Not to Cover

| Content | Why |
|---------|-----|
| Column/type listings | In schema; wastes tokens, risks staleness |
| FK documentation | Declared in schema; drifts silently |
| Migration history | Agents read migration files directly |
| Obvious descriptions | "`user_email`: the user's email" — no value |

## Good vs Bad

**Bad:** "`orders` has columns `id`, `user_id`, `status`, `total`."
**Good:** "Order `status`: `draft → confirmed → shipped`. `cancelled` only from `draft`/`confirmed`."

**Bad:** "FK from `orders.user_id` to `users.id`."
**Good:** "Orders reach `inventory_service` via `order.confirmed` event — no FK."

## Self-Check

1. Every entity described by domain meaning, not columns?
2. Implicit relationships (events, queues, polymorphic) documented?
3. Would removing any line leave an agent no worse off?
4. State machines and soft-delete conventions explicit?
5. Every denormalisation names source of truth and sync?
6. Accurate against the current schema right now?

Research: `research/data-model-specs.md`.
