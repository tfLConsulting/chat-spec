# Reframed Rubrics Draft

All 13 artifact types with reframed questions. Changes marked.

## Universal changes (all types)

1. `current` weight: 1 → 2, language strengthened
2. Two universal penalty items added to every rubric:

| Item | W | Question |
|------|---|----------|
| code_restatement | -2 | Contains significant content that restates what an AI would infer from code, configs, or standard conventions? |
| contradictions | -3 | Contains claims that contradict the current codebase? |

Penalty scoring: YES = problem exists → subtract weight. Evaluator must cite examples. Raw score clamped at 0.

## Reframing principles

- Items asking about "listing/describing all X" → reframe to focus on value-add beyond code
- Items already about non-obvious, rationale, or inherently non-code info → keep unchanged
- `non_obvious` items → unchanged (already quality-oriented)
- W:1 items that are inherently about things not in code → keep or lightly tighten
- Changes noted with ← marker

---

## Architecture

| Item | W | Question |
|------|---|----------|
| components | 3 | Identifies system components, focusing on roles and interactions not obvious from code structure? ← |
| relationships | 3 | Explains component communication patterns not obvious from imports or direct dependencies? ← |
| data_flow | 2 | Documents data flow for key operations where the path is non-obvious or crosses system boundaries? ← |
| non_obvious | 2 | Documents decisions, rationale, and constraints not inferable from code? |
| boundaries | 2 | Defines component ownership and responsibilities beyond what directory structure conveys? ← |
| entry_points | 1 | Identifies system entry points, especially non-obvious ones (background jobs, event handlers, CLIs)? ← |
| persistence | 1 | Documents data storage decisions and constraints not evident from config and schema files? ← |
| external | 1 | Notes external service dependencies and constraints not captured in package manifests? ← |
| constraints | 1 | Notes architectural constraints or trade-offs? |
| current | **2** | Accurately reflects the current codebase with no stale or contradictory claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 18. Penalties: up to -5.

## Features

| Item | W | Question |
|------|---|----------|
| core_features | 3 | Describes major features, focusing on behaviour and intent not obvious from the UI or code? ← |
| feature_detail | 3 | Explains what features do and why, beyond what a user or AI would discover through exploration? ← |
| user_perspective | 2 | Explains features from the user's perspective, including non-obvious workflows and expected outcomes? ← |
| non_obvious | 2 | Documents non-obvious behaviour and design rationale not inferable from code? |
| feature_status | 2 | Indicates feature status (complete, in progress, planned, deprecated)? ← |
| interactions | 1 | Describes feature interactions and dependencies not obvious from code? ← |
| scope | 1 | Distinguishes what the project does vs. doesn't? |
| entry_flows | 1 | Describes key user workflows, especially where the path is non-obvious? ← |
| current | **2** | Accurately reflects current feature state with no stale or contradictory claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Conventions

| Item | W | Question |
|------|---|----------|
| code_style | 3 | Documents code style rules that differ from language/framework defaults or linter config? ← |
| patterns | 3 | Describes key codebase patterns an AI would not infer from reading a few files? ← |
| non_obvious | 2 | Captures conventions that deviate from defaults or would be guessed wrong? |
| naming | 2 | Defines naming conventions where they deviate from standard practices or vary by context? ← |
| testing | 2 | Documents testing expectations beyond what test config and existing tests convey? ← |
| file_structure | 2 | Explains file/directory conventions not obvious from inspecting the tree? ← |
| anti_patterns | 1 | Lists things to avoid? |
| examples | 1 | Includes code examples demonstrating non-obvious conventions? ← |
| current | **2** | Accurately reflects actual codebase practices with no aspirational or outdated claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 18. Penalties: up to -5.

## Development Context

| Item | W | Question |
|------|---|----------|
| project_purpose | 3 | Explains what the project is and why it exists? |
| key_decisions | 3 | Documents major decisions and their rationale? |
| non_obvious | 2 | Captures gotchas, surprises, things that look wrong but aren't? |
| setup | 2 | Documents dev environment setup steps not obvious from package manifests and config files? ← |
| build_run | 2 | Documents build, run, and test commands, especially non-standard steps or prerequisites? ← |
| history | 1 | Provides relevant project history? |
| team_context | 1 | Notes team structure or ownership? |
| roadmap | 1 | Indicates what's planned? |
| current | **2** | Accurately reflects the current state with no stale or contradictory claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

---

## API Docs

| Item | W | Question |
|------|---|----------|
| endpoint_list | 3 | Lists API endpoints, focusing on purpose and behaviour not obvious from route definitions? ← |
| request_response | 3 | Documents request/response contracts, especially implicit validations, transformations, and side effects not visible in type definitions? ← |
| authentication | 2 | Describes authentication and authorization approach, including non-obvious token flows or permission logic? ← |
| non_obvious | 2 | Documents non-obvious API behaviour, edge cases, and rate limits not inferable from code? |
| error_handling | 2 | Documents error responses beyond standard framework defaults, including domain-specific error codes? ← |
| examples | 1 | Includes example requests and responses for non-trivial operations? ← |
| versioning | 1 | Documents API versioning approach? |
| pagination | 1 | Describes pagination, filtering, or sorting patterns where they deviate from framework defaults? ← |
| current | **2** | Accurately reflects the current API with no stale endpoints or outdated schemas? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Data Model

| Item | W | Question |
|------|---|----------|
| entities | 3 | Describes major entities, focusing on purpose and domain meaning not obvious from schema definitions? ← |
| relationships | 3 | Explains entity relationships, especially implicit ones not expressed as foreign keys or ORM associations? ← |
| storage | 2 | Documents storage decisions and rationale not obvious from config (e.g., why certain data lives in Redis vs Postgres)? ← |
| non_obvious | 2 | Documents non-obvious schema decisions, denormalisation, or constraints not inferable from code? |
| lifecycle | 2 | Documents data lifecycle where it involves non-obvious state transitions, soft deletes, or archival logic? ← |
| validation | 1 | Documents validation rules beyond what schema constraints and model validations express? ← |
| migration | 1 | Describes migration approach and history? |
| indexes | 1 | Notes index choices and query optimisation decisions with rationale? ← |
| current | **2** | Accurately reflects the current schema with no stale entities or outdated relationships? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Deployment

| Item | W | Question |
|------|---|----------|
| environments | 3 | Describes deployment environments, including differences between them not obvious from config files? ← |
| deploy_process | 3 | Documents deployment steps, especially manual interventions or prerequisites not captured in CI/CD config? ← |
| infrastructure | 2 | Documents infrastructure decisions and constraints not captured in IaC or config files? ← |
| non_obvious | 2 | Documents non-obvious deployment constraints, gotchas, or manual steps not inferable from config? |
| configuration | 2 | Documents environment configuration and secrets management approach beyond what config files express? ← |
| monitoring | 1 | Documents monitoring, alerting, or observability setup? |
| rollback | 1 | Describes rollback procedures? |
| ci_cd | 1 | Documents CI/CD pipeline decisions and constraints beyond what workflow files express? ← |
| current | **2** | Accurately reflects the current deployment setup with no stale or contradictory claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Dependencies

| Item | W | Question |
|------|---|----------|
| key_deps | 3 | Identifies key dependencies, focusing on why they were chosen rather than restating what package manifests show? ← |
| rationale | 3 | Explains why key dependencies were chosen over alternatives? |
| non_obvious | 2 | Documents non-obvious dependency constraints, incompatibilities, or pinning reasons? |
| version_policy | 2 | Describes version update policy (pinned, ranges, auto-update)? |
| internal | 1 | Documents internal packages with purpose and relationships not obvious from the monorepo structure? ← |
| security | 1 | Notes security-relevant dependency considerations? |
| removal | 1 | Identifies dependencies being phased out? |
| current | **2** | Accurately reflects current dependencies with no outdated or removed packages listed? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 15. Penalties: up to -5.

## Design System

| Item | W | Question |
|------|---|----------|
| components | 3 | Describes UI components, focusing on usage guidelines and constraints not obvious from the component code? ← |
| visual_standards | 3 | Documents visual standards and rationale, focusing on rules not captured in CSS variables or theme config? ← |
| patterns | 2 | Describes UI patterns and when to use each, especially where the choice is non-obvious? ← |
| non_obvious | 2 | Documents non-obvious design decisions, accessibility requirements, or platform-specific deviations? |
| accessibility | 2 | Documents accessibility requirements and implementation approach beyond framework defaults? ← |
| responsive | 1 | Describes responsive breakpoints and behaviour where they deviate from framework defaults? ← |
| states | 1 | Documents component state patterns and conventions not obvious from implementations? ← |
| tokens | 1 | Documents design token decisions and naming conventions beyond what the token files express? ← |
| current | **2** | Accurately reflects the current design system with no deprecated patterns or stale references? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Security Model

| Item | W | Question |
|------|---|----------|
| auth_flow | 3 | Describes the authentication flow, including token lifecycle and session management not obvious from code? ← |
| authorization | 3 | Documents authorization model and permission logic, especially rules not expressed in code or middleware? ← |
| non_obvious | 2 | Documents non-obvious security decisions, trust boundaries, or known accepted risks? |
| data_protection | 2 | Documents data protection decisions and compliance requirements not evident from code? ← |
| threat_model | 2 | Identifies key threats and mitigations? |
| secrets | 1 | Documents secrets management approach and rotation policies beyond what config files express? ← |
| audit | 1 | Describes audit logging or compliance tracking? |
| incident | 1 | Documents incident response procedures? |
| current | **2** | Accurately reflects current security implementation with no stale claims? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 17. Penalties: up to -5.

## Performance

| Item | W | Question |
|------|---|----------|
| slos | 3 | Defines performance targets (response time, throughput, availability)? |
| bottlenecks | 3 | Identifies known bottlenecks and performance-critical paths with context not obvious from profiling? ← |
| non_obvious | 2 | Documents non-obvious performance constraints, trade-offs, or counter-intuitive optimisations? |
| caching | 2 | Documents caching strategy decisions and invalidation rules not evident from cache config? ← |
| scaling | 2 | Documents scaling approach and capacity constraints not captured in infrastructure config? ← |
| monitoring | 1 | Describes performance monitoring and alerting? |
| benchmarks | 1 | Documents benchmark results or baseline measurements? |
| current | **2** | Accurately reflects current performance characteristics with no outdated measurements? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 16. Penalties: up to -5.

## Integration

| Item | W | Question |
|------|---|----------|
| services | 3 | Identifies external services and APIs, focusing on purpose and constraints not obvious from client code? ← |
| purpose | 3 | Explains integration purpose and business rationale beyond what the client code reveals? ← |
| non_obvious | 2 | Documents non-obvious integration behaviour, retry logic, or failure modes? |
| auth | 2 | Documents integration authentication, including credential provisioning and rotation not evident from code? ← |
| data_flow | 2 | Documents data flow to/from integrations, especially transformations and constraints not visible in code? ← |
| failure | 1 | Documents failure handling decisions and SLA implications beyond what retry config expresses? ← |
| testing | 1 | Documents how integrations are tested (mocks, sandboxes)? |
| current | **2** | Accurately reflects current integrations with no deprecated or disconnected services listed? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 16. Penalties: up to -5.

## AI Context Config

| Item | W | Question |
|------|---|----------|
| tool_config | 3 | Provides configuration for the project's primary AI tool? |
| project_context | 3 | Gives the AI tool essential project context it would not infer correctly from code alone? ← |
| non_obvious | 2 | Contains project-specific instructions an AI would get wrong without guidance? |
| conventions_ref | 2 | References key conventions that differ from defaults or that the AI would guess wrong? ← |
| scope | 1 | Defines what the AI should and shouldn't do in this project? |
| examples | 1 | Includes examples of good patterns or expected output? |
| maintenance | 1 | Can be updated when project patterns change? |
| current | **2** | Accurately reflects the current project state with no stale instructions? ← |
| code_restatement | -2 | *(universal penalty)* |
| contradictions | -3 | *(universal penalty)* |

Max: 15. Penalties: up to -5.

---

## New level boundaries

With `current` at W:2, each max increases by 1. Boundaries adjusted proportionally, keeping the same semantic meaning per level.

| Type | Max | L1 | L2 | L3 | L4 | L5 |
|------|-----|----|----|----|----|-----|
| Architecture | 18 | 0-3 | 4-7 | 8-11 | 12-15 | 16-18 |
| Features | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Conventions | 18 | 0-3 | 4-7 | 8-11 | 12-15 | 16-18 |
| Dev Context | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| API Docs | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Data Model | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Deployment | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Dependencies | 15 | 0-3 | 4-6 | 7-9 | 10-12 | 13-15 |
| Design System | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Security | 17 | 0-3 | 4-7 | 8-10 | 11-14 | 15-17 |
| Performance | 16 | 0-3 | 4-6 | 7-10 | 11-13 | 14-16 |
| Integration | 16 | 0-3 | 4-6 | 7-10 | 11-13 | 14-16 |
| AI Config | 15 | 0-3 | 4-6 | 7-9 | 10-12 | 13-15 |
