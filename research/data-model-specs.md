# Data Model Specification Research

Research into what makes data model documentation effective or harmful for AI coding tools, how agents discover schemas, and what content improves vs. degrades agent performance on data-related tasks. Conducted 2026-02-26.

## Core Finding

AI agents can read schema files, ORM models, and migration history — but they cannot infer why the schema looks the way it does. Documenting column definitions, table names, or foreign keys that already exist in code wastes tokens and measurably hurts performance. The documentation that helps is narrow: implicit relationships, denormalisation rationale, state machine transitions, soft-delete conventions, business-term definitions, and storage tier decisions. In text-to-SQL benchmarks, adding semantic descriptions to otherwise uninformative column names improved accuracy from 58% to 86% — but only when the descriptions contained information not already conveyed by the column name itself.

## Key Evidence

### Data model documentation that hurts

**ETH Zurich AGENTbench (2026):** Context files that restated code-inferable information decreased agent performance by 2-3%. Schema files (ORM models, Prisma schemas, migration files) are already in the codebase. Repeating column names, types, and explicit foreign keys in documentation adds token cost without new information.
- Source: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study

**Augment Code failure patterns (2025):** "Data Model Mismatches" is identified as one of eight systematic failure patterns in AI-generated code. Agents assume data structures rather than validating actual schemas. "Most production outages from AI-generated code trace back to data model mismatches that passed code review because they looked plausible." The fix is not more schema documentation — it is runtime validation and type interfaces that the agent can read from code.
- Source: https://www.augmentcode.com/guides/debugging-ai-generated-code-8-failure-patterns-and-fixes

**HumanLayer CLAUDE.md guide (2025):** Explicitly advises against including database schema details in shared context files. Including instructions about "how to structure a new database schema" will "distract the model when you're working on something else." Instead, put schema context in separate referenced files that agents can pull when relevant.
- Source: https://www.humanlayer.dev/blog/writing-a-good-claude-md

### What data model documentation actually helps

**Tiger Data semantic catalog (2025-2026):** Without semantic context, 42% of LLM-generated SQL queries missed critical filters or misunderstood relationships. A semantic catalog with natural language descriptions for tables, columns, functions, and business rules dramatically reduced errors. Key insight: schema element names alone lack sufficient meaning — terms like "payment_aborts" vs "refunds" require human explanation that retrieval algorithms cannot disambiguate. Business logic "is often not encoded into the schema" and must be explicitly documented.
- Source: https://www.tigerdata.com/blog/the-database-new-user-llms-need-a-different-database

**Column descriptions study (arXiv:2408.04691, 2024):** Evaluated six LLMs on 1,534 text-to-SQL pairs from BIRD-Bench. Column descriptions improved execution accuracy modestly in normal conditions (~10% relative improvement). But when column names were replaced with uninformative labels (A1, A2, etc.), gold-standard descriptions recovered ~20% absolute accuracy. Conclusion: descriptions add value proportional to how much information is missing from the schema itself. Describing what is already obvious from a well-named column wastes tokens.
- Source: https://arxiv.org/html/2408.04691v1

**Price & Corral Data metadata study (2025):** Column descriptions and sample values provide the greatest performance gains for text-to-SQL. Concise, descriptive metadata outperforms verbose documentation. Explicit relationship documentation between tables improves accuracy. Strategic metadata inclusion — focusing on semantic clarity rather than exhaustive documentation — yields optimal results.
- Source: https://corraldata.com/wp-content/uploads/2025/01/PriceCorralData2025_MetadataTextSQL.pdf

**Select Star (2025):** LLMs face three gaps when working with databases: lack of schema knowledge, missing business context (terms like "revenue" or "active user" have specific definitions LLMs don't know), and no usage patterns (no examples of successful queries). The fix requires three layers: schema context, business context, and usage context.
- Source: https://www.selectstar.com/resources/text-to-sql-llm

**Codified Context paper (arXiv:2602.20478, 2026):** Across 283 sessions on a 108k-line codebase, domain-specific specifications containing known failure modes, correctness requirements, and design intent reduced errors significantly. Over half the content in effective agent specs comprised codebase facts and known failure modes, not behavioural instructions. Stale specifications actively misled agents — legacy fields caused agents to wire calculations through deprecated paths.
- Source: https://arxiv.org/html/2602.20478

### How agents discover data models (and where they fail)

**Schema file reading:** Agents can read `schema.prisma`, SQLAlchemy models, Django models, Rails migrations, and raw SQL DDL. Prisma specifically recommends providing the schema file as context, enabling agents to generate "precise, schema-driven outputs." This is the baseline — agents are generally competent at parsing explicit schema definitions.
- Source: https://www.prisma.io/docs/orm/more/ai-tools/cursor

**Cross-service dependency blindness:** Agents "can't track dependencies across service boundaries, understand rollback requirements, or handle the complexity of distributed systems." Teams discover their actual dependency graphs contain "3x more connections than documented." Implicit dependencies through message queues or event systems are regularly missed.
- Source: https://www.augmentcode.com/guides/8-ai-coding-agents-that-actually-accelerate-database-schema-migrations

**Convention inference failure:** Agents struggle with implicit conventions: soft-delete patterns (a `deleted_at` column silently filtering all queries), polymorphic associations (a `commentable_type`/`commentable_id` pair), and multi-tenant scoping (an implicit `tenant_id` filter). These patterns are framework conventions, not schema-level declarations. An agent "coming up with the 'wrong' solution often simply doesn't know the conventions it should be adhering to."
- Source: https://blog.thepete.net/blog/2025/05/22/why-your-ai-coding-assistant-keeps-doing-it-wrong-and-how-to-fix-it/

**MCP-based schema access:** Prisma MCP Server and Postgres-MCP expose database schemas directly to agents through tool interfaces. This gives agents live schema introspection rather than relying on documentation. The trend is toward agents discovering schemas at runtime rather than reading documentation about them.
- Source: https://softcery.com/lab/softcerys-guide-agentic-coding-best-practices

### Denormalisation rationale and storage decisions

**Denormalisation and AI:** Fewer tables and fewer joins reduce the chance of wrong join conditions and hallucinated relationships in AI-generated queries. But without documentation of why data was denormalised, agents cannot distinguish intentional redundancy from schema errors. The recommendation: "document every denormalisation rule that you have applied" including "the reasons behind denormalizing any table or column, as well as the trade-offs and risks involved."
- Source: https://www.linkedin.com/advice/3/what-best-way-document-database-normalization

**Architecture Decision Records for data decisions:** ADRs work well for data model decisions because they combine "enough structure to ensure key points are addressed, but in natural language, which is perfect for LLMs." The Context/Decision/Consequences format captures why a storage choice was made, what alternatives were rejected, and what constraints apply.
- Source: https://blog.thestateofme.com/2025/07/10/using-architecture-decision-records-adrs-with-ai-coding-assistants/

## Common Failure Modes in Data Model Documentation

| Failure Mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Restating column definitions | Column names, types, and FK constraints are in the schema file; duplicating them wastes tokens | AGENTbench: restating code-inferable info decreases performance 2-3% |
| Duplicating migration history | Agents can read migration files directly; summarising them adds noise | AGENTbench: LLM-generated context files "redundant with existing docs" |
| Missing business term definitions | Terms like "revenue," "active user," "churn" have project-specific meanings the agent cannot guess | Select Star: business context is one of three critical gaps |
| Undocumented soft-delete conventions | Agent generates queries without `WHERE deleted_at IS NULL` filters | Convention inference failure: agents don't infer framework conventions |
| Undocumented state machines | Agent allows invalid state transitions (e.g., `draft` -> `archived` skipping `published`) | No schema-level declaration of valid transitions exists for agents to discover |
| Missing denormalisation rationale | Agent "fixes" intentional redundancy or creates conflicting denormalised copies | Without documented rationale, intentional redundancy looks like schema errors |
| Stale entity documentation | Agent wires logic through deprecated fields or removed relationships | Codified Context: legacy stat fields caused incorrect wiring |
| Missing cross-service relationships | Agent changes a table without understanding downstream consumers via queues/events | Augment: teams find "3x more connections than documented" |
| Undocumented polymorphic patterns | Agent misinterprets `_type`/`_id` column pairs or creates competing patterns | Convention-based, not schema-declared; agents cannot discover from DDL |

## What a Data Model Spec Should Contain

Based on this research, data model documentation should contain only what agents cannot discover from schema files, ORM models, and migration history.

| Content Type | Why It Helps | What to Document |
|-------------|-------------|-----------------|
| Business term glossary | Agents don't know project-specific definitions | Map domain terms to tables/columns: "active user = users WHERE last_login > 30 days AND status = 'active'" |
| Entity state machines | No schema-level declaration of valid transitions | States, valid transitions, guard conditions, side effects of each transition |
| Soft-delete and scoping conventions | Framework conventions not visible in DDL | Which models use soft delete, the filter column, any models that hard-delete, multi-tenant scoping rules |
| Implicit relationships | Not expressible as foreign keys | Event-driven dependencies, message queue consumers, polymorphic associations, cross-service data flows |
| Denormalisation rationale | Prevents agents from "fixing" intentional redundancy | Which fields are denormalised, why, what the source of truth is, what keeps them in sync |
| Storage tier decisions | Not inferable from schema files | Why data lives in Postgres vs Redis vs S3; caching strategy; read replica routing |
| Sample queries for ambiguous operations | Agents benefit from usage context | Common join patterns, aggregation approaches, filter combinations for key business operations |
| Known data gotchas | Prevents recurring agent errors | Nullable fields that should never be null in practice, timezone handling, encoding issues, precision requirements |

## What a Data Model Spec Should NOT Contain

| Anti-Pattern | Why It Hurts |
|-------------|-------------|
| Column name/type listings | Already in schema files; restating wastes tokens and risks going stale |
| Foreign key documentation | Already declared in schema; duplicating risks contradicting code |
| Migration summaries | Agents can read migration files; summaries add noise |
| Full ERD reproductions in text | Too verbose for context windows; agents parse schema files more reliably |
| Index documentation | Already in schema DDL; restatement adds no value |
| Default value listings | Already in schema/ORM model definitions |
| Obvious column descriptions | "user_email: the email address of the user" wastes tokens; only describe columns where the name is misleading or ambiguous |

## Implications for Data Model Rubrics

1. **Business terms and implicit logic are the highest-value content.** The 58% to 86% accuracy improvement from semantic descriptions demonstrates that the gap is meaning, not structure. Rubrics should weight business-term definitions and implicit-relationship documentation heavily.

2. **State machines and lifecycle documentation fill a genuine gap.** No schema format declares valid state transitions. This is always implicit in application code, scattered across service layers and callbacks. Documenting it prevents invalid transitions — a common agent failure mode.

3. **Restatement of schema content should be penalised, not rewarded.** Column listings, type definitions, and FK documentation are actively harmful. A rubric that rewards "completeness" of entity documentation will incentivise the wrong behaviour.

4. **Freshness is critical for data model docs.** Stale entity documentation causes agents to wire logic through deprecated fields. The Codified Context paper directly documents this failure mode. Data model specs should be treated as load-bearing: wrong is worse than missing.

5. **Progressive disclosure works better than monolithic docs.** Put data model specs in separate referenced files rather than in shared context. Agents should pull schema context only when working on data-related tasks. The HumanLayer guidance and the Codified Context paper's three-tier architecture both support this approach.

6. **Agents are increasingly discovering schemas at runtime.** MCP servers for Prisma, Postgres, and other databases give agents live schema introspection. Documentation should complement runtime discovery, not duplicate it.
