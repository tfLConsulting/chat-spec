# Design System Documentation for AI Agent Consumption

Research into what makes design system and component library documentation effective for AI coding agents, and common failure modes. Conducted 2026-02-28.

## Core Finding

AI agents default to generating new components from generic training data rather than reusing existing design system components. The primary intervention is a machine-readable component manifest — structured metadata exposing component APIs, prop types, usage examples, and token bindings — delivered at the point of generation rather than embedded in prose documentation. When agents receive this context, code quality improves, token usage drops, and design system compliance increases. Without it, agents produce duplicative, inconsistent UI code that ignores the team's established patterns.

## Key Evidence

### Agents ignore design systems without structured manifests

**Storybook Design Systems with Agents RFC (2025):** AI agents building UI "typically ignore existing design systems and generate their own components using familiar tools like shadcn and Tailwind." The RFC proposes Component Manifests — JSON metadata exposing component names, descriptions, prop specifications (keys, types, defaults), usage examples derived from stories, and related component relationships. Research behind the RFC found that "LLMs usages of design systems improve greatly when they have structured knowledge about what's available." Adding prop information, usage examples, and type definitions progressively improved output quality beyond simple component lists.
- Source: https://github.com/storybookjs/ds-mcp-experiment-reshaped/discussions/1

**Storybook MCP (2025-2026):** The Component Manifest provides "an optimized payload, allowing an agent to parse a component's interface, its variants, its design token bindings, and example usage — in a fraction of the tokens it would take to parse the source file." Agents working from this context complete tasks faster with fewer tokens. A self-healing loop lets agents run component tests (interaction and accessibility), see failures, and fix their own code — reducing developer intervention to post-test review.
- Source: https://storybook.js.org/blog/storybook-mcp-sneak-peek/

**Codrops analysis (2025):** Without manifest context, agents waste 50K-100K tokens per task loading full codebases, default to "average components" misaligned with team conventions, generate hallucinated component states with wrong props, and create new code instead of reusing existing components.
- Source: https://tympanus.net/codrops/2025/12/09/supercharge-your-design-system-with-llms-and-storybook-mcp/

### Component discovery requires explicit, queryable metadata

**Figma MCP (2025-2026):** Figma's MCP server sends contextual information — components, styles, and variables — to AI agents when a frame is inspected. Automated design system rule generation scans the codebase and outputs a structured rules file outlining token definitions, component libraries, style hierarchies, and naming conventions. This acts as "a system-level guide for the agent."
- Source: https://developers.figma.com/docs/figma-mcp-server/
- Source: https://developers.figma.com/docs/figma-mcp-server/structure-figma-file/

**Figma's Code Connect:** Described as "the #1 way to get consistent component reuse in code." Without it, "the model is guessing" — agents generate duplicate components rather than referencing existing codebase implementations. Code Connect links Figma components to actual code, including folder structures, so agents maintain organisational consistency and proper component inheritance.
- Source: https://developers.figma.com/docs/figma-mcp-server/structure-figma-file/

**Vercel v0 Registry (2025-2026):** A registry is "a distribution specification designed to pass context from your design system to AI Models." The shadcn Registry provides structured JSON sharing components, blocks, and design tokens. v0 is "specifically trained on the default implementations of shadcn/ui components" and may struggle with customisations to underlying primitives — documenting deviations from defaults is essential.
- Source: https://v0.app/docs/design-systems
- Source: https://vercel.com/blog/ai-powered-prototyping-with-design-systems

### Design tokens need semantic naming and machine-readable formats

**W3C Design Tokens Specification 2025.10:** The first stable version provides a vendor-neutral JSON interchange format. Token files use `application/design-tokens+json` media type with `$`-prefixed properties (`$value`, `$type`, `$description`). Supports theming via `$extends`, modern colour spaces (Display P3, Oklch), and cross-platform code generation for iOS, Android, web, and Flutter. Reference implementations exist in Style Dictionary, Tokens Studio, and Terrazzo.
- Source: https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/

**Practitioner guidance:** Semantic token names (describing purpose and context, not visual appearance) outperform raw values for AI consumption. Figma variables should be used for spacing, colour, radius, and typography — hardcoded values in Figma prevent proper token extraction by MCP tools. Auto Layout maps directly to CSS Flexbox properties, producing cleaner AI-generated code than absolute positioning.
- Source: https://blog.logrocket.com/ux-design/design-to-code-with-figma-mcp/

**AI token management:** Supernova's AI features auto-generate semantic token names from primitives. UXPin's Merge technology bridges design and code through AI-driven token synchronisation. The trend is toward tokens as the primary interface between design intent and generated code.
- Source: https://medium.com/@marketingtd64/design-tokens-and-ai-scaling-ux-with-dynamic-systems-316afa240f6f

### Accessibility is AI's weakest area in UI generation

**W4A 2025 study (Accessibility Barriers in LLM-Generated Code):** Evaluated LLM-generated UIs against WCAG 2.1 using both accessibility-agnostic and accessibility-oriented prompts. Accessibility-oriented prompts increased WCAG success counts and reduced violation rates, but persistent barriers remained — particularly in semantic structure. The dominant failures were: language of page, info and relationships (WCAG 1.3.1), and name/role/value. A related study of 88 developers found 84% of AI-generated websites contained perceivability issues (text resizing, colour contrast).
- Source: https://dl.acm.org/doi/10.1145/3744257.3744266

**The Design System Guide framework (2025):** Accessibility is the weakest area in AI component generation. Common failures include: divs instead of semantic HTML, icon-only buttons without `aria-label`, focus indicators removed without replacement, and disabled states missing both `disabled` and `aria-disabled` attributes. Explicit accessibility prompting improved focus visibility by 233% in one study.
- Source: https://learn.thedesignsystem.guide/p/the-ultimate-ai-component-generation

**American Foundation for the Blind (2025):** Automated testing and AI cannot fully solve digital accessibility. A majority of WCAG 3.3 Input Assistance success criteria go undetected by automated tools. Compliance does not automatically equal usability.
- Source: https://afb.org/blog/entry/missing-human-touch-1

### Component composition rules prevent structural errors

**Romina Kavcic's 16-Step Framework (2025):** A structured checklist for AI component generation. Key findings: naming Figma layers improved results by 30%; using Figma variables for colours and spacing enabled perfect MCP token extraction; building in stages (components first, then patterns, then screens) achieved 95% success vs. 60% for single-shot generation. Version specificity matters — "Use React 18.2+ with functional components" beats generic guidance.
- Source: https://learn.thedesignsystem.guide/p/the-ultimate-ai-component-generation

**Composition documentation needs:** Component relationships (which components compose others), nesting rules, and dependency hierarchies must be explicitly documented. Without explicit templates for file structure, agents invent their own organisation. Responsive behaviour requires explicit breakpoint documentation — generated code does not automatically handle mobile visibility without additional prompting.
- Source: https://blog.logrocket.com/ux-design/design-to-code-with-figma-mcp/

**Iterative generation outperforms single-shot:** Vercel recommends breaking designs into smaller frames for AI processing. Individual component frames avoid "potential size and dimension errors" and ensure AI can process each component more effectively. Gradually building up to complete pages by assembling components produces better results than generating entire pages at once.
- Source: https://vercel.com/blog/working-with-figma-and-custom-design-systems-in-v0

### AI rules files and context engineering for design systems

**Cursor rules and CLAUDE.md patterns (2025-2026):** Teams document component patterns in `.cursor/rules/` or `CLAUDE.md` files — specifying tech stacks, component conventions, and design system references. These should include runnable code examples rather than vague guidance. The AGENTS.md convention is gaining traction as a tool-agnostic location for AI instructions, with symlinks to tool-specific files. Claude ignores frontmatter while Cursor uses it, allowing cross-tool compatibility.
- Source: https://docs.cursor.com/context/rules
- Source: https://frontendmasters.com/courses/pro-ai/setup-cursor-rules/

**Enterprise design system AI guidelines:** IBM Carbon for AI, SAP Fiori, Microsoft HAX Toolkit, PatternFly, and ServiceNow Horizon all publish AI-specific guidelines. Common themes: transparency about AI capabilities/limitations, progressive disclosure (What/Why/How), clear labelling of AI-generated content, and error correction support. Anti-patterns include implementing AI features without addressing genuine user needs and designing systems that reduce rather than support human agency.
- Source: https://www.supernova.io/blog/top-6-examples-of-ai-guidelines-in-design-systems

### Code duplication is the dominant failure mode

**MIT Technology Review (2025):** AI-assisted coding is linked to 4x more code cloning. For the first time, developers paste code more often than they refactor or reuse it. AI tools "seldom consider the design and architecture of your application" and "do not reuse or refactor existing code but build everything from the ground up."
- Source: https://www.technologyreview.com/2025/12/15/1128352/rise-of-ai-coding-developers-2026/

**Code quality at scale:** While AI reduces obvious bugs and security vulnerabilities, "code smells" — harder-to-pinpoint maintenance problems — make up over 90% of issues in AI-generated code. AI-generated tests verify assumptions rather than developer intent, rarely including edge cases, domain-specific constraints, or legacy integration scenarios.
- Source: https://devops.com/why-ai-based-code-generation-falls-short/

## Common Failure Modes

### Component discovery and reuse
| Mode | Impact |
|------|--------|
| No component manifest or registry | Agent creates new components instead of reusing existing ones |
| Manifest lacks prop types and defaults | Agent guesses props, producing render errors |
| No usage examples from real stories | Agent composes components incorrectly |
| Missing component relationship mapping | Agent nests incompatible components or duplicates wrappers |
| Stale component documentation | Agent uses deprecated APIs or removed variants |

### Design tokens and theming
| Mode | Impact |
|------|--------|
| Hardcoded values instead of tokens | Agent produces non-themeable, brittle output |
| No semantic token naming | Agent cannot map purpose to correct token |
| Missing theme/mode documentation | Agent ignores dark mode or responsive variants |
| Token format not machine-readable | Agent cannot extract or apply tokens automatically |

### Accessibility
| Mode | Impact |
|------|--------|
| No explicit accessibility requirements | Agent produces divs instead of semantic HTML |
| Missing ARIA pattern documentation | Icon buttons lack labels, roles omitted |
| No accessibility test integration | Failures go undetected in generation loop |
| Focus management undocumented | Agent removes or ignores focus indicators |

### Composition and layout
| Mode | Impact |
|------|--------|
| No responsive breakpoint documentation | Agent ignores mobile/tablet layout needs |
| Missing spacing/layout conventions | Agent uses arbitrary values instead of system spacing |
| Single-shot full-page generation | 60% success vs. 95% for staged component-first approach |
| No file structure template | Agent invents its own organisation |

## Implications for Design System Spec Guidance

### What documentation helps agents most

| Documentation type | Agent impact | Evidence strength |
|-------------------|-------------|-------------------|
| Component manifest (props, types, defaults, examples) | Enables discovery and correct usage | Strong — Storybook RFC, v0 registry |
| Design tokens in W3C format | Enables theming and cross-platform generation | Strong — W3C spec, tool implementations |
| Usage examples from real stories/code | Prevents incorrect composition | Strong — Anthropic, Storybook benchmarks |
| Accessibility requirements per component | Reduces WCAG violations | Moderate — W4A study, practitioner reports |
| Component relationship graph | Prevents duplication and incorrect nesting | Moderate — practitioner evidence |
| Responsive breakpoint definitions | Enables adaptive layouts | Moderate — practitioner evidence |
| File structure conventions | Prevents organisational drift | Moderate — practitioner evidence |

### What documentation hurts or wastes tokens

| Documentation type | Problem |
|-------------------|---------|
| Visual design descriptions ("rounded corners, blue header") | Restates what tokens and props already encode |
| Full component source code in docs | Bloats context without adding signal |
| Generic component overviews | Agent infers structure from code |
| Exhaustive variant screenshots | Not parseable by text-based agents |
