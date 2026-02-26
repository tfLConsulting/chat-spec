# API Documentation for AI Agent Consumption

Research into what makes API documentation effective for AI agents and coding tools, and common failure modes. Conducted 2026-02-26.

## Core Finding

AI agents perform endpoint selection via semantic matching against API descriptions. The quality of descriptions — not their volume — determines whether an agent picks the correct endpoint, fabricates parameters, or hallucinates non-existent APIs entirely. Documenting the non-obvious (implicit validations, side effects, permission logic) is critical because agents cannot infer these from schema alone.

## Key Evidence

### Descriptions drive tool selection

**Agent Experience (AX) research (2025-2026):** When an agent decides which endpoint to call, it performs something close to semantic search against descriptions. A sparse description like "Gets the data" loses to "Returns a paginated list of invoices filtered by status and date range, sorted by created_at descending. Requires accounting:read scope." Every field, enum value, and endpoint needs a description — it is the signal the agent uses to route.
- Source: https://www.apideck.com/blog/api-design-principles-agentic-era

**OpenAI function calling guidance:** Descriptions should pass the "intern test" — if someone unfamiliar with the system couldn't correctly use the function from only the documentation, the description needs improvement. Use enums and structured parameters to make invalid states impossible rather than relying on descriptions to prevent misuse. Functions count against context limits, so descriptions should be informative but not verbose.
- Source: https://developers.openai.com/api/docs/guides/function-calling/

**Anthropic tool use:** Tool search matches against names and descriptions, so clear, descriptive definitions improve discovery accuracy. When tool libraries are large, parameter accuracy becomes critical — and examples solve that more elegantly than longer schema descriptions. Recommended: 1-5 examples per tool showing minimal, partial, and full specification patterns.
- Source: https://www.anthropic.com/engineering/advanced-tool-use

### Tool hallucination scales with tool count and poor documentation

**Tool Hallucination Taxonomy (arXiv:2412.04141, 2024):** Two main types: *tool selection hallucination* (invoking an unrelated tool or fabricating a tool name) and *tool usage hallucination* (wrong parameter types, fabricated parameter values). Baseline hallucination rates on StableToolBench reached 61.5-91.1% depending on task complexity. Models making excessive tool calls introduce more hallucinations — minimising unnecessary invocations improves reliability.
- Source: https://arxiv.org/html/2412.04141v1

**API Hallucination in Code Generation (arXiv:2407.09726, 2024):** LLMs achieve 93.66% valid invocations for high-frequency APIs but only 38.58% for low-frequency ones. Over 50% of failures for low-frequency APIs are non-existent API usage (hallucinated endpoints). Documentation Augmented Generation (DAG) improves low-frequency performance but degrades high-frequency performance due to irrelevant augmentation. Selective retrieval (DAG++) resolves this: 8.20% overall improvement while maintaining balance across frequency tiers.
- Source: https://arxiv.org/html/2407.09726v1

**AWS Agent Hallucination Techniques (2025):** Semantic tool selection — filtering relevant tools using embeddings before presenting them to agents — reduced hallucinations by up to 86.4% and cut token costs by 89%. Prompt engineering alone cannot enforce constraints; agents ignore docstring instructions because they are processed as text, not executable rules.
- Source: https://dev.to/aws/stop-ai-agent-hallucinations-4-essential-techniques-2i94

### Tool definition bloat fills context

**Anthropic tool search (2025):** A typical multi-server setup (GitHub, Slack, Sentry, Grafana, Splunk) can consume ~55K tokens in tool definitions before the agent does any work. Tool search reduces this by over 85%, loading only the 3-5 tools actually needed. Claude's ability to correctly pick the right tool degrades significantly once you exceed 30-50 available tools.
- Source: https://www.anthropic.com/engineering/advanced-tool-use

**OpenAI tool scaling guidance (2025):** Setups with fewer than ~100 tools and ~20 arguments per tool are considered in-distribution. Performance depends on prompt design and task complexity beyond these bounds. Strict mode (`strict: true`) eliminates type mismatches and missing fields for production agents.
- Source: https://developers.openai.com/api/docs/guides/function-calling/

### What AI tool makers recommend for API descriptions

**Anthropic:** Define tools with clear names, descriptions, and input schemas. Provide examples alongside schemas so models learn correct usage patterns. Use `strict: true` for guaranteed schema conformance.
- Source: https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview

**OpenAI:** Instead of "Search the web", use "Search the web for current information. Use this when the user asks about recent events, news, or data that may have changed since training." Tell the model exactly when and when *not* to use each function. Avoid making the model fill in arguments you already possess.
- Source: https://developers.openai.com/api/docs/guides/function-calling/

**MCP (Model Context Protocol):** Build tools optimised for specific user goals, not wrappers around full API schemas. Fewer well-designed tools outperform many granular ones for agents with small context windows. Document capabilities clearly — actions, parameters, and expected results.
- Source: https://modelcontextprotocol.io/

### Emerging standards: llms.txt

**llms.txt (proposed 2024, growing adoption 2025-2026):** A markdown-based file at `/llms.txt` distilling a site's documentation into a concise format, reducing token consumption by 90%+ compared to parsing HTML. Two variants: `llms.txt` (compact overview with links) and `llms-full.txt` (complete content). Not yet officially adopted by major AI companies, but Anthropic, Stripe, and others publish llms.txt files.
- Source: https://llmstxt.org/

**Stripe's implementation:** Their llms.txt includes an "Instructions for Large Language Model Agents" section that steers AI coding assistants toward preferred integration patterns and away from deprecated or legacy endpoints. Adding `.md` to any Stripe documentation URL provides plain text with fewer formatting tokens.
- Source: https://docs.stripe.com/building-with-llms

### Authentication complexity for AI agents

**OAuth and agent identity (2025):** AI agents lack mechanisms to propagate user identity through execution chains. Dynamic client registration, token audience binding, and PII-based token issuance all introduce non-obvious failure modes. If PII elements are included in LLM training data, the model may hallucinate them during token issuance flows.
- Source: https://stytch.com/blog/agent-to-agent-oauth-guide/
- Source: https://www.ietf.org/archive/id/draft-oauth-ai-agents-on-behalf-of-user-01.html

## Common Failure Modes in API Documentation for AI

### Description quality failures
| Mode | Impact |
|------|--------|
| Sparse or missing descriptions | Agent performs semantic matching on empty signals; selects wrong endpoint |
| Generic descriptions ("Gets data") | Insufficient disambiguation between similar endpoints |
| Undocumented enum values | Agent guesses or fabricates values |
| Undocumented side effects | Agent triggers unintended mutations |
| Stale deprecated endpoints in spec | Agent selects deprecated paths over current ones |

### Schema and structure failures
| Mode | Impact |
|------|--------|
| Missing required/optional distinction | Agent omits required params or fabricates optional ones |
| No examples for non-trivial payloads | Agent hallucinates parameter structure from name alone |
| Inconsistent error response formats | Agent cannot build reliable error handling |
| Undocumented rate limits | Agent triggers 429s without backoff strategy |
| Pagination deviations undocumented | Agent assumes standard patterns, misses cursor-based or offset quirks |

### Operational documentation failures
| Mode | Impact |
|------|--------|
| Auth flows documented for humans only | Agent cannot execute multi-step token exchanges |
| No documentation_url in error responses | Agent cannot self-correct on failure |
| Implicit validation rules | Agent submits payloads that pass schema but fail business rules |
| Versioning strategy undocumented | Agent calls wrong API version or mixes versions |

## Implications for chat-spec API Docs Rubric

### What the research validates in current rubric items

| Rubric item | Research support |
|-------------|-----------------|
| endpoint_list (W:3) | Descriptions are the primary signal for agent endpoint selection; high weight justified |
| request_response (W:3) | Implicit validations and side effects are invisible to schema parsing; high weight justified |
| authentication (W:2) | Agent auth has unique failure modes (identity propagation, dynamic registration); weight justified |
| non_obvious (W:2) | Edge cases and rate limits cause silent agent failures; weight justified |
| error_handling (W:2) | Agents need consistent error formats and documentation_url fields to self-correct |
| examples (W:1) | Research strongly supports examples — Anthropic recommends 1-5 per tool; **weight may be too low** |
| code_restatement (-2) | Tool definition bloat directly degrades selection accuracy; penalty justified |
| contradictions (-3) | Stale endpoints in specs cause agents to select deprecated paths; penalty justified |

### Gaps the research reveals

| Gap | Evidence |
|-----|----------|
| Examples weight too low | Anthropic, OpenAI both cite examples as highest-leverage improvement for tool accuracy; W:1 may undervalue this |
| No explicit "description quality" signal | Agent selection accuracy depends on description specificity, not just presence |
| Error self-correction pattern undocumented | Including `documentation_url` in error responses enables agent self-healing — not captured in rubric |
| Tool count / scope concern | API docs that expose too many endpoints without grouping degrade agent selection accuracy |
