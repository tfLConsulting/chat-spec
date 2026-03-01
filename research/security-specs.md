# Security Specification Research

Research into what makes security documentation effective or harmful for AI coding tools, and what content helps agents make safe decisions vs what they already infer from code. Conducted 2026-02-26.

## Core Finding

AI agents can read middleware config, auth library usage, CORS headers, and security package settings directly from code — but they cannot infer trust boundaries, permission model rationale, security invariants that break silently, or which auth flows are intentionally restrictive vs accidentally incomplete. Documenting framework defaults (Helmet headers, bcrypt rounds, CSRF tokens) wastes tokens and measurably hurts performance. The documentation that helps is narrow: the permission model and its rationale, trust boundary locations, auth flow decisions that would look wrong without context, security invariants that have no code-level enforcement, and known areas where agents systematically generate insecure code.

## Key Evidence

### What security documentation hurts

**ETH Zurich AGENTbench (2026):** Context files restating code-inferable information decreased agent performance by 2-3%. Security middleware config (Helmet options, CORS settings, passport strategies) is already in code files. Repeating it in docs adds token cost without new information.
- Source: https://umesh-malik.com/blog/agents-md-ai-coding-agents-study

**OpenSSF Security-Focused Guide (2025):** The guide found that telling an AI system "it is an expert often makes it perform poorly or worse" on security tasks. Generic security persona instructions degrade output quality. What works instead: specific, language-level directives about dangerous functions, parameterised queries, and constant-time comparisons. The guide explicitly structures recommendations as concrete rules, not awareness checklists.
- Source: https://best.openssf.org/Security-Focused-Guide-for-AI-Code-Assistant-Instructions.html

**Addy Osmani spec analysis (2025):** Across 2,500+ analysed agent configurations, "never commit secrets or API keys" was the single most common helpful constraint. This suggests the highest-value security instructions are short, specific prohibitions — not comprehensive frameworks.
- Source: https://addyosmani.com/blog/good-spec/

### What agents systematically get wrong on security

**Veracode GenAI Code Security Report (2025):** Tested 100+ LLMs across 80 security-focused coding tasks. AI-generated code introduced security flaws in 45% of tests. Key failure rates by CWE:
- CWE-80 (XSS): 86% failure rate
- CWE-117 (Log Injection): 88% failure rate
- CWE-89 (SQL Injection): 20% failure rate
- CWE-327 (Weak Cryptography): 14% failure rate
- Java had a 72% security failure rate; Python/C#/JavaScript ranged 38-45%
- Source: https://www.veracode.com/blog/genai-code-security-report/

**Endor Labs vulnerability analysis (2025):** Over 40% of AI-generated code contains security flaws. When security guidance is absent from prompts, agents routinely bypass authentication and authorisation entirely. Hard-coded credentials (CWE-798) and broken access control (CWE-284) appear frequently. AI-generated code was 1.88x more likely to have improper password handling and 1.91x more likely to have insecure object references than human code.
- Source: https://www.endorlabs.com/learn/the-most-common-security-vulnerabilities-in-ai-generated-code

**Augment Code failure patterns (2025):** "Security Vulnerabilities" is one of eight systematic failure patterns. The pattern: code functions correctly but fails securely. AI-generated exception handling frequently exposes sensitive system information through unfiltered error messages. The fix is systematic: security scanners pre-commit, structured error boundaries that sanitise messages.
- Source: https://www.augmentcode.com/guides/debugging-ai-generated-code-8-failure-patterns-and-fixes

**Cloud Security Alliance (2025):** AI coding assistants "don't inherently understand your application's risk model, internal standards, or threat landscape." The disconnect introduces "not just insecure lines of code, but logic flaws, missing controls, and inconsistent patterns."
- Source: https://cloudsecurityalliance.org/blog/2025/07/09/understanding-security-risks-in-ai-generated-code

### What security documentation actually helps

**OpenSSF Recursive Criticism and Improvement (2025):** Rather than loading comprehensive security documentation, the guide recommends iterative review. Asking the AI to "review your previous answer and find problems" then "improve your answer" can improve security "up to an order of magnitude" with just two iterations. This suggests that process instructions (how to self-check) outperform content instructions (what to know about security).
- Source: https://best.openssf.org/Security-Focused-Guide-for-AI-Code-Assistant-Instructions.html

**Codified Context paper (arXiv:2602.20478, 2026):** Over half the content in effective agent specifications comprised codebase facts and known failure modes, not behavioural instructions. When a category of task repeatedly required re-explaining the same domain knowledge, that knowledge was codified into a specification. The three-tier architecture (hot memory, specialised agents, cold memory) applies directly: security invariants belong in hot memory; auth flow details in specialised context retrieved when relevant.
- Source: https://arxiv.org/html/2602.20478

**Anthropic Agentic Coding Trends Report (2026):** Any engineer can now leverage AI to perform security reviews, hardening, and monitoring that previously required specialised expertise. But: "organisations that treat security as an afterthought will face consequences that no productivity gain can offset." The report flags embedding security architecture from day one as a strategic priority.
- Source: https://resources.anthropic.com/2026-agentic-coding-trends-report

### Trust boundaries and permission models

**OWASP Threat Modeling (2025):** Trust boundaries are important because calls crossing them need authentication and authorisation, and data crossing them must be treated as untrusted. These boundaries are architectural decisions invisible in individual code files — an agent reading a single service cannot see where the trust boundary lies.
- Source: https://owasp.org/www-community/Threat_Modeling_Process

**Oso authorization research (2025):** Traditional RBAC built for predictable human roles is insufficient for AI agents. Agents need authorisation that adapts in real time: fine-grained, just-in-time decisions rather than static role mappings. The recommendation: route every tool invocation through an external authorisation service where the policy, not the model, decides whether an action runs.
- Source: https://www.osohq.com/learn/why-rbac-is-not-enough-for-ai-agents

**Auth0 AI agent access control (2025):** For AI agents, access control requires understanding the delegation chain: which user authorised the agent, what scopes were granted, and what the agent is doing on whose behalf. This delegation context is never visible in code — it exists in configuration, token claims, and organisational policy.
- Source: https://auth0.com/blog/access-control-in-the-era-of-ai-agents/

**Curity API security trends (2026):** OAuth integrations are moving toward token exchange for least-privileged access and JWT-assertion grants to cross security boundaries. Machine identity and agent authorisation will dominate 2026. How to enable SSO across trust domains and federate authorisation without compromising security remains an open challenge.
- Source: https://curity.io/blog/api-security-trends-2026/

### What goes stale fastest in security docs

**Salesloft/Drift OAuth breach (2025):** Stale OAuth scopes and inconsistent rotation turned a single OAuth theft into a multi-SaaS blast radius. Session settings and IP policies rarely get revisited. Most teams cannot enforce maximum lifetimes or rotation SLAs. The incident demonstrated that security documentation around auth providers, permission models, and secret rotation deteriorates as systems evolve and teams change.
- Source: https://www.oasis.security/blog/the-salesloft-oauth-compromise-what-it-changed-and-what-to-do-next

**Augment Code cross-service dependencies (2025):** Teams discover their actual dependency graphs contain "3x more connections than documented." Implicit dependencies through message queues or event systems are regularly missed. An agent changing one service cannot see downstream security implications.
- Source: https://www.augmentcode.com/guides/8-ai-coding-agents-that-actually-accelerate-database-schema-migrations

**Port internal documentation study (2025):** Only 6% of engineers update documentation daily; up to 43% update docs once per week. A single outdated line in a setup guide can lead to misconfigurations and security risks. Security docs are especially vulnerable because auth provider changes, permission model updates, and secret rotation schedules change faster than teams update docs.
- Source: https://www.port.io/blog/backstage-techdocs-vs-port

### Anti-patterns in security documentation

**OWASP Top 10 as awareness, not standard (2025):** OWASP explicitly states the Top 10 is "an awareness document, not a full standard." Several items (A06:2025 Insecure Design, logging/monitoring effectiveness) cannot be tested with automated tools. Dumping the OWASP Top 10 into a security spec gives an agent generic awareness it already has from training data, while missing the project-specific decisions that actually prevent vulnerabilities.
- Source: https://owasp.org/Top10/2025/0x03_2025-Establishing_a_Modern_Application_Security_Program/

**Helmet/CORS/middleware defaults:** AI agents can read `helmet()` configuration, CORS middleware options, and auth library setup directly from code. Documenting that "we use Helmet for HTTP security headers" or "CORS is configured for approved origins" adds zero information. The only value is documenting why specific non-default configurations exist (e.g., "CSP allows unsafe-inline because of legacy analytics script X — remove after Q3 migration").

**Generic security checklists:** Security checklists that list "use parameterised queries," "validate input," "encrypt at rest" restate knowledge that all modern LLMs have from training data. The OpenSSF found that specific, language-level rules (e.g., "In Python, do not use exec/eval on user input; use subprocess with shell=False") outperform generic directives.
- Source: https://best.openssf.org/Security-Focused-Guide-for-AI-Code-Assistant-Instructions.html

## Common Failure Modes in Security Documentation

| Failure Mode | Mechanism | Evidence |
|-------------|-----------|----------|
| Restating middleware config | Helmet options, CORS settings, passport strategies visible in code; duplicating wastes tokens | AGENTbench: code-inferable info decreases performance 2-3% |
| OWASP checklist dumping | Generic top-10 items are in LLM training data; restating adds noise without project specificity | OWASP: Top 10 is awareness, not a standard; cannot be tested automatically |
| Missing trust boundaries | Agent cannot see where one service's trust zone ends and another begins from individual files | OWASP: calls crossing boundaries need auth; data crossing must be validated |
| Undocumented permission rationale | Agent generates permissive access because restrictive pattern looks like a bug | Endor Labs: 1.91x more insecure object references without guidance |
| Stale auth provider docs | OAuth scopes, token lifetimes, rotation schedules change; docs lag | Salesloft breach: stale scopes enabled multi-SaaS blast radius |
| Generic security persona instructions | "You are a security expert" degrades output | OpenSSF: expert persona makes AI perform worse on security tasks |
| Missing error handling boundaries | Agent exposes system internals in error messages | Augment: exception handling frequently exposes sensitive information |
| Undocumented delegation chains | Which user authorised the agent, what scopes, acting on whose behalf — invisible in code | Auth0: delegation context exists in config and policy, not code |
| Missing cross-service security dependencies | Agent changes one service without seeing downstream security implications | Augment: teams find 3x more connections than documented |

## What a Security Spec Should Contain

Based on this research, security documentation should contain only what agents cannot discover from code, middleware config, and library defaults.

| Content Type | Why It Helps | What to Document |
|-------------|-------------|-----------------|
| Permission model and rationale | Agents generate permissive code without understanding why restrictions exist | Who can do what, why restrictions exist, which resources are sensitive, what the escalation path is |
| Trust boundaries | Invisible in individual code files; architectural decision | Where boundaries are, what crosses them, what must be validated at each boundary |
| Auth flow decisions | Intentionally restrictive patterns look like bugs without context | Which auth provider, why that choice, what flows exist, what's intentionally forbidden |
| Security invariants | No code-level enforcement; break silently | Rules that must always hold (e.g., "user can only access own org's data"), not enforced by a single check |
| Known agent failure areas | Agents systematically fail on XSS, log injection, error exposure | Project-specific rules: "Always sanitise log output," "Never expose stack traces," "Use parameterised queries for table X" |
| Non-default security config rationale | Only non-default config needs explanation | Why CSP has specific exceptions, why CORS allows specific origins, why a timeout is unusually long |
| Secret management conventions | How secrets flow is not visible from code alone | Where secrets live, how they rotate, what can never be logged or returned in responses |
| Cross-service security contracts | Agent modifying one service cannot see downstream security requirements | What data other services trust from this service, what auth they expect, what breaks if contracts change |

## What a Security Spec Should NOT Contain

| Anti-Pattern | Why It Hurts |
|-------------|-------------|
| Middleware configuration details | Already in code; restating risks staleness and wastes tokens |
| OWASP Top 10 checklists | Generic awareness already in LLM training data; no project specificity |
| "Use HTTPS," "encrypt at rest," "validate input" | Universal knowledge; no agent changes behaviour based on this |
| Auth library API documentation | Agent reads library docs and code; restating adds noise |
| Standard HTTP security header lists | Agent reads Helmet/equivalent config directly |
| Generic threat model frameworks | STRIDE/DREAD categories without project-specific threats are unfalsifiable |
| "We follow security best practices" | Empty claim; no actionable information |
| Password policy details that match library defaults | Agent reads bcrypt rounds, password validators from code |

## Implications for Security Rubrics

1. **Trust boundaries and permission rationale are the highest-value content.** Agents cannot infer these from code. A security spec missing trust boundaries is critically incomplete even if it lists every middleware package in use.

2. **Security invariants fill a genuine gap.** Rules like "user can only access own organisation's data" or "admin endpoints require MFA" have no single enforcement point in code. They are distributed across middleware, route guards, database queries, and frontend checks. Documenting them prevents agents from creating bypass paths.

3. **Restatement of security config should be penalised.** Documenting Helmet options, CORS settings, or passport strategies is actively harmful — it wastes tokens and goes stale. A rubric rewarding "comprehensive security coverage" incentivises the wrong behaviour.

4. **Process instructions outperform content instructions.** The OpenSSF finding that iterative self-review improves security "up to an order of magnitude" suggests specs should include review instructions ("check for XSS in any user-facing output") rather than comprehensive vulnerability taxonomies.

5. **Staleness is the critical security risk.** Auth provider changes, scope modifications, and rotation schedule updates happen faster than documentation updates. Security specs must be treated as load-bearing: wrong is far worse than missing. The Salesloft breach demonstrated real-world consequences of stale auth documentation.

6. **Agent-specific security failures are predictable and documentable.** Veracode data shows XSS (86% failure), log injection (88% failure), and error information exposure are systematic. Project-specific rules addressing these known failure patterns have outsized impact.
