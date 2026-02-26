# Deployment Documentation for AI Coding Tools

Research into what makes deployment documentation effective or harmful when consumed by AI coding agents, and what content helps vs. hurts agent performance in deployment-related tasks. Conducted 2026-02-26.

## Core Finding

Deployment documentation helps AI agents only when it captures information invisible in IaC configs, CI/CD workflow files, and Dockerfiles. IaC is "executable documentation" --- the code already expresses desired state, resource dependencies, and configuration. What agents cannot discover: why environments differ, what manual interventions exist outside pipelines, which constraints are invisible in config, and what to do when deployments fail. Documentation that restates Terraform resources or Kubernetes manifests is measurably harmful; documentation that captures operational judgment and non-obvious constraints is high-value.

## Key Evidence

### IaC is self-documenting --- supplementary docs must add new information

Infrastructure-as-Code configurations express desired state, resource properties, dependencies, and access rules. When IaC is maintained, there is less demand for separate infrastructure documentation because the code itself should never be out of date. The documentation value lies in what IaC cannot express: why a particular architecture was chosen, what constraints drove the decision, and what happens when the code does not match reality.
- Source: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code
- Source: https://www.redhat.com/en/topics/automation/what-is-infrastructure-as-code-iac

### Infrastructure drift is the deployment equivalent of stale docs

When live infrastructure diverges from IaC configuration, subsequent IaC operations revert valid changes, causing misconfigurations or failures. Three common drift scenarios: emergency incident response bypasses IaC, performance tuning happens interactively, and debugging adds temporary observability resources without updating config. An automated reconciliation system (NSync) found that standard AI agents achieved only 0.49 pass@1 accuracy reconciling drift, suffering from "context dilution" when processing verbose planning outputs. Specialised tooling raised this to 0.97 pass@3 by treating reconciliation as targeted program repair rather than general reasoning.
- Source: https://arxiv.org/html/2510.20211v1

### Agents without deployment context produce fragile code

Without operational context, AI agents generate code that assumes every service is always up and every request succeeds. Agents typically overlook structured logging, tracing, and metrics unless explicitly prompted. Generated applications lack handling for transient network failures, rate-limited APIs, and database outages. Agents "fill gaps arbitrarily, often with simplistic decisions optimised for immediate functionality" rather than production resilience.
- Source: https://vfunction.com/blog/vibe-coding-architecture-ai-agents/

### Vibe-coded deployments demonstrate missing-context failures

The Moltbook incident (January 2025) demonstrated catastrophic failure from missing deployment documentation: AI agents generated functional database schemas but never enabled Row Level Security, leaking over 1.5 million API keys and exposing user databases. Agents were observed removing validation checks, relaxing database policies, and disabling authentication flows to resolve runtime errors --- actions that would be prevented by documented security constraints and deployment checklists.
- Source: https://www.isyncevolution.com/blog/ai-code-slop-crisis-vibe-coding-security-risks

### Observability is table stakes but documentation lags

LangChain's 2025 State of Agent Engineering survey (1,300+ respondents) found 89% of teams have implemented observability for agents, with 94% of production teams having some form of it and 71.5% having full tracing. However, critical monitoring knowledge frequently lives only in people's heads or outdated documentation. The recommended minimum: tool failure rate, p95 tool latency, retry storms, cost per request, then side effect anomalies and safety escalations.
- Source: https://www.langchain.com/state-of-agent-engineering

### Rollback complexity exceeds what agents assume

Reverting code is straightforward, but agents modify persistent data and trigger actions across multiple systems. Effective rollback requires version tagging for prompts and code, database migration reversal, and integration state restoration. Feature flags (e.g., LaunchDarkly) complete rollbacks in under 30 seconds versus 3-5 minutes for container redeployment. This operational knowledge --- that rollback is not just "deploy the previous version" --- is invisible in CI/CD configs.
- Source: https://datagrid.com/blog/cicd-pipelines-ai-agents-guide
- Source: https://www.groovyweb.co/blog/cicd-pipeline-ai-agent-teams-guide

### Environment differences are the classic deployment trap

The Twelve-Factor App's dev/prod parity principle identifies three gaps: time (code written vs deployed), personnel (devs write, ops deploy), and tools (dev stack vs production stack). Tiny incompatibilities between backing services cause code that passed tests to fail in production. The goal is "relevant parity" --- aspects that affect application behaviour must be consistent; aspects that don't (instance sizes, redundancy) can differ for cost. Environment variables configure differences, but the software itself should be identical.
- Source: https://12factor.net/dev-prod-parity
- Source: https://www.statsig.com/perspectives/dev-vs-staging-vs-prod

## What AI Tool Makers Recommend

### Anthropic (Claude Code)

CLAUDE.md should include "developer environment quirks (required env vars)" and "common gotchas or non-obvious behaviors." Deployment workflow instructions Claude could not infer (branch naming conventions, deployment processes, code review requirements) should be added and committed to version control. The include/exclude test: anything inferable from code, standard conventions, or config files should be excluded.
- Source: https://code.claude.com/docs/en/best-practices

### Pulumi (Agent Skills for DevOps)

Procedural knowledge must be documented, not inferred. "Skills are the user manuals and standard operating procedures." Key areas requiring explicit documentation: deployment conventions and safe refactoring procedures, OIDC credential flows and secret store integration, diagnostic steps and rollback protocols with severity models, and security policies (NetworkPolicies, RBAC, encryption). Without this context, "generic AI assistance produces code that looks reasonable but breaks conventions."
- Source: https://www.pulumi.com/blog/top-8-claude-skills-devops-2026/
- Source: https://www.pulumi.com/blog/pulumi-agent-skills/

### OpenAI (Codex / AGENTS.md)

AGENTS.md guides the agent on repository expectations, including which commands to run for testing and how to adhere to project practices. Codex agents "perform best when provided with configured dev environments, reliable testing setups, and clear documentation." However, the current AGENTS.md specification does not include deployment-specific sections --- it focuses on code-level guidance.
- Source: https://developers.openai.com/codex/guides/agents-md/

### GitHub (Spec Kit / Copilot)

Spec-driven development maps "specification to actual system architecture by defining which services, modules, data flows, state logic, and observability hooks will be affected." The technical plan stage declares architecture, stack, and constraints. Per-workspace settings allow different projects to have different Copilot behaviour (models, instructions, feature flags).
- Source: https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/

### Trail of Bits (Security-focused)

Security audit workflows bundle reference material into skills: analysis checklists, vulnerability patterns, example outputs, and decision logic. Configuration operates in two layers: global (organisation-wide) and project-specific. Sandboxing and permission deny-rules enforce constraints deterministically rather than relying on documentation compliance.
- Source: https://github.com/trailofbits/claude-code-config

## Common Failure Modes in Deployment Documentation

| Failure Mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Restating IaC/config | Terraform, Kubernetes manifests, and Dockerfiles already express desired state; repeating wastes tokens | IaC described as "executable documentation" --- HashiCorp, Red Hat |
| Stale deployment docs | Live infrastructure drifts from docs after emergency fixes, performance tuning, debugging | NSync: standard agents achieve 0.49 accuracy reconciling drift |
| Missing security constraints | Agents disable auth, skip RLS, relax policies to resolve errors when constraints are undocumented | Moltbook breach: 1.5M API keys leaked from missing RLS |
| Rollback oversimplification | Documenting "deploy previous version" ignores data migrations, feature flags, integration state | Rollback requires version tagging, migration reversal, state restoration |
| Undocumented environment differences | "Works in dev, fails in prod" from backing service differences invisible in code | Twelve-Factor App: tiny incompatibilities cause production failures |
| Generic operational guidance | "Monitor your services" is not actionable; agents need specific metrics, thresholds, escalation paths | Pulumi: without specifics, agents "produce code that looks reasonable but breaks conventions" |
| Missing manual steps | Pre-deployment prerequisites, manual approval gates, DNS changes, certificate rotation invisible in CI/CD | Agents assume automated pipelines handle everything |
| Verbose deployment runbooks | Long prose runbooks dilute context; agents lose focus on reconciliation objectives | NSync: "context dilution" from verbose planning outputs |

## What the Deployment Rubric Should Reward

Based on this research, mapping findings to rubric items:

| Rubric Item | Research Support |
|-------------|-----------------|
| environments (W:3) | High value. Environment differences cause production failures. Only document what differs from config: backing service incompatibilities, feature flags, data differences, scaling behaviour |
| deploy_process (W:3) | High value. Manual interventions, approval gates, DNS changes, certificate management --- anything outside CI/CD workflow files. Agents assume full automation unless told otherwise |
| infrastructure (W:2) | Document decisions not expressed in IaC: why this architecture, what alternatives were rejected, what constraints drove choices. Do not restate resource definitions |
| non_obvious (W:2) | Core of what works. Gotchas, race conditions, ordering dependencies, capacity limits, cold-start behaviour --- things that only surface in production |
| configuration (W:2) | Secret rotation schedules, OIDC flows, credential injection mechanisms, env vars that must be set but are not in config files. Never document secret values |
| monitoring (W:1) | Specific metrics, thresholds, and escalation paths. Not "we use Datadog" but "alert on p95 latency > 500ms on /api/checkout, escalate to #payments-oncall" |
| rollback (W:1) | Multi-step rollback procedures: code revert + data migration reversal + feature flag reset + integration state. Not just "deploy previous version" |
| ci_cd (W:1) | Pipeline decisions beyond what workflow files express: why jobs are ordered this way, what manual gates exist, what flaky tests are known |
| code_restatement (-2) | Essential penalty. IaC is self-documenting. Restating Terraform resources, Kubernetes manifests, or Dockerfile instructions adds noise and drift risk |
| contradictions (-3) | Essential penalty. Infrastructure drift means documented state and actual state diverge. Agents achieve only 0.49 accuracy when docs contradict reality |

## Implications for Deployment Guidance

1. **IaC is the source of truth for "what." Document the "why" and "watch out."** Terraform files, Kubernetes manifests, and CI/CD workflows already express infrastructure state and pipeline steps. Deployment docs should capture decisions (why this region, why this instance type), constraints (rate limits, cold-start latency, compliance requirements), and manual steps (certificate rotation, DNS propagation, approval gates) that exist outside config.

2. **Environment documentation should describe differences, not each environment.** Listing every environment variable in every environment restates config. Describe what differs between environments and why: different backing services, feature flags, data masking, scaling policies, or security constraints.

3. **Rollback is multi-dimensional, not a single operation.** Document the full rollback procedure: code revert, database migration reversal, feature flag reset, cache invalidation, integration state restoration. Agents assume "deploy previous version" unless told otherwise.

4. **Monitoring docs should be prescriptive, not descriptive.** "We use Prometheus" is inferable from config. "Alert on error rate > 1% on payment endpoints, p95 latency > 2s triggers auto-scaling, page oncall if both fire simultaneously" is actionable deployment knowledge.

5. **Security constraints must be explicit because agents optimise for functionality.** Without documented security requirements, agents disable authentication, skip access controls, and relax policies to resolve errors. Deployment docs should state security invariants as hard constraints, not aspirational guidelines.

6. **Deployment docs rot faster than any other spec category.** Infrastructure changes via console, emergency fixes, and performance tuning all create drift between docs and reality. Treat deployment docs as high-maintenance: review on every infrastructure change, not every code change.
