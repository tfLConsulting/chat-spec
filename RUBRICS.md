# chat-spec Rubrics

Scoring rubrics for all 13 built-in artifact types. Core rubrics (architecture, features, conventions, dev-context) are also in PROTOCOL.md for convenience.

**Scoring:** Each item is YES (weight counts) or NO (0). Sum YES weights. Map total to level 1-5 using the boundaries. Items marked N/A reduce the max score.

---

## Architecture

| Item | W | Question |
|------|---|----------|
| components | 3 | Lists all major system components/services? |
| relationships | 3 | Describes how components communicate? |
| data_flow | 2 | Shows how data moves through the system? |
| non_obvious | 2 | Documents decisions, rationale, and constraints not inferable from code? |
| boundaries | 2 | Defines clear boundaries between components? |
| entry_points | 1 | Identifies system entry points? |
| persistence | 1 | Documents data storage approach? |
| external | 1 | Lists external dependencies? |
| constraints | 1 | Notes architectural constraints or trade-offs? |
| current | 1 | Reflects the actual current state? |

Max: 17. Levels: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

## Features

| Item | W | Question |
|------|---|----------|
| core_features | 3 | Lists all major features/capabilities? |
| feature_detail | 3 | Describes what each feature does? |
| user_perspective | 2 | Explains features from the user's perspective? |
| non_obvious | 2 | Documents non-obvious behaviour and design rationale not inferable from code? |
| feature_status | 2 | Indicates status of each feature? |
| interactions | 1 | Describes how features interact? |
| scope | 1 | Distinguishes what the project does vs. doesn't? |
| entry_flows | 1 | Describes key user workflows? |
| current | 1 | Reflects the actual current state? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Conventions

| Item | W | Question |
|------|---|----------|
| code_style | 3 | Documents code style and formatting rules? |
| patterns | 3 | Describes key patterns in the codebase? |
| non_obvious | 2 | Captures conventions that deviate from defaults or would be guessed wrong? |
| naming | 2 | Defines naming conventions? |
| testing | 2 | Documents testing expectations? |
| file_structure | 2 | Explains file/directory organisation? |
| anti_patterns | 1 | Lists things to avoid? |
| examples | 1 | Includes code examples? |
| current | 1 | Reflects actual codebase practices? |

Max: 17. Levels: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

## Development Context

| Item | W | Question |
|------|---|----------|
| project_purpose | 3 | Explains what the project is and why it exists? |
| key_decisions | 3 | Documents major decisions and their rationale? |
| non_obvious | 2 | Captures gotchas, surprises, things that look wrong but aren't? |
| setup | 2 | Describes how to set up the dev environment? |
| build_run | 2 | Documents how to build, run, and test? |
| history | 1 | Provides relevant project history? |
| team_context | 1 | Notes team structure or ownership? |
| roadmap | 1 | Indicates what's planned? |
| current | 1 | Reflects the actual current state? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

---

## API Docs

Endpoints, schemas, authentication, error handling.

| Item | W | Question |
|------|---|----------|
| endpoint_list | 3 | Lists all API endpoints with methods and paths? |
| request_response | 3 | Documents request/response formats for each endpoint? |
| authentication | 2 | Describes authentication and authorization approach? |
| non_obvious | 2 | Documents non-obvious API behaviour, edge cases, and rate limits not inferable from code? |
| error_handling | 2 | Documents error responses and status codes? |
| examples | 1 | Includes example requests and responses? |
| versioning | 1 | Documents API versioning approach? |
| pagination | 1 | Describes pagination, filtering, or sorting patterns? |
| current | 1 | Reflects the actual current API? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Data Model

Entities, relationships, storage approach.

| Item | W | Question |
|------|---|----------|
| entities | 3 | Lists all major entities/models? |
| relationships | 3 | Describes relationships between entities? |
| storage | 2 | Documents where and how data is stored? |
| non_obvious | 2 | Documents non-obvious schema decisions, denormalisation, or constraints not inferable from code? |
| lifecycle | 2 | Describes data lifecycle (creation, updates, deletion, archiving)? |
| validation | 1 | Documents validation rules and constraints? |
| migration | 1 | Describes migration approach and history? |
| indexes | 1 | Notes key indexes or query optimisation decisions? |
| current | 1 | Reflects the actual current schema? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Deployment

How to deploy, environments, infrastructure.

| Item | W | Question |
|------|---|----------|
| environments | 3 | Lists all deployment environments (dev, staging, prod)? |
| deploy_process | 3 | Describes how to deploy step by step? |
| infrastructure | 2 | Documents infrastructure components and providers? |
| non_obvious | 2 | Documents non-obvious deployment constraints, gotchas, or manual steps not inferable from config? |
| configuration | 2 | Describes environment-specific configuration and secrets management? |
| monitoring | 1 | Documents monitoring, alerting, or observability setup? |
| rollback | 1 | Describes rollback procedures? |
| ci_cd | 1 | Documents CI/CD pipeline? |
| current | 1 | Reflects the actual current deployment setup? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Dependencies

Key dependencies, why chosen, constraints.

| Item | W | Question |
|------|---|----------|
| key_deps | 3 | Lists key dependencies and their purpose? |
| rationale | 3 | Explains why key dependencies were chosen over alternatives? |
| non_obvious | 2 | Documents non-obvious dependency constraints, incompatibilities, or pinning reasons? |
| version_policy | 2 | Describes version update policy (pinned, ranges, auto-update)? |
| internal | 1 | Documents internal packages or shared libraries? |
| security | 1 | Notes security-relevant dependency considerations? |
| removal | 1 | Identifies dependencies being phased out? |
| current | 1 | Reflects the actual current dependency state? |

Max: 14. Levels: 1 (0-2), 2 (3-5), 3 (6-8), 4 (9-11), 5 (12-14)

## Design System

Visual standards, component library, accessibility.

| Item | W | Question |
|------|---|----------|
| components | 3 | Lists all UI components with their purpose? |
| visual_standards | 3 | Documents colours, typography, spacing, and visual rules? |
| patterns | 2 | Describes UI patterns (layout, navigation, forms, feedback)? |
| non_obvious | 2 | Documents non-obvious design decisions, accessibility requirements, or platform-specific deviations? |
| accessibility | 2 | Documents accessibility standards and approach? |
| responsive | 1 | Describes responsive/adaptive behaviour? |
| states | 1 | Documents component states (loading, error, empty, disabled)? |
| tokens | 1 | Lists design tokens or variables? |
| current | 1 | Reflects the actual current design implementation? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Security Model

Authentication, authorization, threat model.

| Item | W | Question |
|------|---|----------|
| auth_flow | 3 | Describes the authentication flow? |
| authorization | 3 | Documents authorization model (roles, permissions, policies)? |
| non_obvious | 2 | Documents non-obvious security decisions, trust boundaries, or known accepted risks? |
| data_protection | 2 | Describes how sensitive data is protected (encryption, masking, retention)? |
| threat_model | 2 | Identifies key threats and mitigations? |
| secrets | 1 | Documents secrets management approach? |
| audit | 1 | Describes audit logging or compliance tracking? |
| incident | 1 | Documents incident response procedures? |
| current | 1 | Reflects the actual current security implementation? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Performance

SLOs, bottlenecks, optimisation constraints.

| Item | W | Question |
|------|---|----------|
| slos | 3 | Defines performance targets (response time, throughput, availability)? |
| bottlenecks | 3 | Identifies known bottlenecks or performance-critical paths? |
| non_obvious | 2 | Documents non-obvious performance constraints, trade-offs, or counter-intuitive optimisations? |
| caching | 2 | Describes caching strategy and invalidation? |
| scaling | 2 | Documents scaling approach (horizontal, vertical, auto-scaling)? |
| monitoring | 1 | Describes performance monitoring and alerting? |
| benchmarks | 1 | Documents benchmark results or baseline measurements? |
| current | 1 | Reflects the actual current performance characteristics? |

Max: 15. Levels: 1 (0-3), 2 (4-6), 3 (7-9), 4 (10-12), 5 (13-15)

## Integration

Third-party services, APIs consumed, webhooks.

| Item | W | Question |
|------|---|----------|
| services | 3 | Lists all external services and APIs consumed? |
| purpose | 3 | Explains what each integration is used for? |
| non_obvious | 2 | Documents non-obvious integration behaviour, retry logic, or failure modes? |
| auth | 2 | Describes how each integration is authenticated? |
| data_flow | 2 | Documents what data flows to/from each integration? |
| failure | 1 | Describes failure handling (retries, fallbacks, circuit breakers)? |
| testing | 1 | Documents how integrations are tested (mocks, sandboxes)? |
| current | 1 | Reflects the actual current integrations? |

Max: 15. Levels: 1 (0-3), 2 (4-6), 3 (7-9), 4 (10-12), 5 (13-15)

## AI Context Config

CLAUDE.md, .cursorrules — how the project configures AI tools.

| Item | W | Question |
|------|---|----------|
| tool_config | 3 | Provides configuration for the project's primary AI tool? |
| project_context | 3 | Gives the AI tool essential project context (structure, patterns, constraints)? |
| non_obvious | 2 | Contains project-specific instructions an AI would get wrong without guidance? |
| conventions_ref | 2 | References or includes key conventions the AI should follow? |
| scope | 1 | Defines what the AI should and shouldn't do in this project? |
| examples | 1 | Includes examples of good patterns or expected output? |
| maintenance | 1 | Can be updated when project patterns change? |
| current | 1 | Reflects the actual current project state? |

Max: 14. Levels: 1 (0-2), 2 (3-5), 3 (6-8), 4 (9-11), 5 (12-14)
