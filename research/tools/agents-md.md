# AGENTS.md

- **Website:** [agents.md](https://agents.md/)
- **Repo:** [agentsmd/agents.md](https://github.com/agentsmd/agents.md)
- **Governance:** Agentic AI Foundation under the Linux Foundation

## What it is

An open standard for providing context and instructions to AI coding agents, regardless of which tool they are. Just standard Markdown, no required fields. Recommended sections: Project Overview, Build & Test Commands, Code Style, Testing, Security.

## Adoption

- **60,000+ open source projects** by late 2025
- Supported by: OpenAI Codex, Google Jules, Cursor, Aider, RooCode, Zed, GitHub Copilot, Claude Code, Gemini CLI, Factory, Devin, Amp
- 40.6% of repos with any AI config file (in 217k-repo survey)
- Donated to Linux Foundation alongside Anthropic's MCP

## How it works

- `AGENTS.md` in project root and subdirectories
- Agents read the nearest file in the directory tree
- Supports folder structure with `index.md` for complex projects
- Six core areas: commands, testing, project structure, code style, git workflow, boundaries

## Empirical effectiveness (ETH Zurich)

- Developer-written AGENTS.md: **~4% improvement** in agent performance
- LLM-generated AGENTS.md: **~3% decrease** (negative effect)
- Inference cost increase: **20%+**
- Style guides and testing conventions often added unnecessary complexity
- Recommendation: include only minimal, task-relevant requirements

Despite modest gains, HN community viewed 4% as significant for "just a simple markdown file."

## Strengths

- Cross-tool (the only real standard)
- Linux Foundation governance
- Simple (just Markdown)
- Backed by major players

## Limitations

- Adoption still emerging
- Research suggests limited actual effectiveness
- No quality scoring or validation
- No composability standard for how configs from dependencies should merge

## Relevance to chat-spec

AGENTS.md is a **format** for AI instructions. Chat-spec could potentially *generate and maintain* AGENTS.md files as one of its outputs. The ETH Zurich finding that quality matters more than existence validates chat-spec's focus on maturity scoring.

## Sources

- [AGENTS.md website](https://agents.md/)
- [GitHub repo](https://github.com/agentsmd/agents.md)
- [GitHub blog on writing AGENTS.md](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [ETH Zurich evaluation](https://arxiv.org/abs/2602.11988)
- [OpenAI Codex guide](https://developers.openai.com/codex/guides/agents-md/)
- [InfoQ coverage](https://www.infoq.com/news/2025/08/agents-md/)
- [HN discussion on effectiveness](https://news.ycombinator.com/item?id=47034087)
