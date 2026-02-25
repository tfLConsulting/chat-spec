# Config Sync Tools

Tools that solve the "maintain one source, fan out to many AI config formats" problem.

## Ruler

- **Repo:** [intellectronica/ruler](https://github.com/intellectronica/ruler)
- Single `.ruler/` directory as source of truth, auto-distributes to tool-specific config files
- Also handles MCP server propagation and .gitignore automation
- Keeps generated files out of git

## ContextPilot

- **Repo:** [contextpilot-dev/contextpilot](https://github.com/contextpilot-dev/contextpilot)
- Generates and maintains AI context files from codebase analysis
- Tracks work sessions so AI tools understand project
- Early stage

## rulesync

- **Repo:** [jpcaparas/rulesync](https://github.com/jpcaparas/rulesync)
- Syncs instruction files across AI assistants

## ai-rules-sync

- **Repo:** [lbb00/ai-rules-sync](https://github.com/lbb00/ai-rules-sync)
- Synchronize, manage, share AI rules across projects and teams

## syncai

- **Repo:** [flowmitry/syncai](https://github.com/flowmitry/syncai)
- Sync AI rules and ignore files between agents

## knowhub

- [Medium article](https://medium.com/@yujiisobe/introducing-knowhub-share-ai-assistant-rules-across-repos-17fb6b09c114)
- Share AI assistant rules across repositories

## rulebook-ai

- **Repo:** [botingw/rulebook-ai](https://github.com/botingw/rulebook-ai)
- Universal managed template supporting 10+ AI tools
- Includes "memory bank" concept

## Agent Rules Builder

- **Website:** [agentrulegen.com](https://www.agentrulegen.com/)
- Free web app: select language + framework + AI tool, generates tailored rules file
- 1000+ community rules

## ClaudeMDEditor

- **Website:** [claudemdeditor.com](https://www.claudemdeditor.com/)
- $13 visual editor for CLAUDE.md, .cursorrules, copilot-instructions.md
- Auto-finds config files across repos, pre-made sections, skills/rules library

## Agnix

- [HN: Show HN](https://news.ycombinator.com/item?id=46983879)
- Lints AI agent configs (CLAUDE.md, skills, MCP, hooks) — early attempt at validation

## Relevance to chat-spec

These tools solve distribution/sync. Chat-spec could potentially generate content that these tools then distribute. Or chat-spec could handle distribution itself as an output format concern.
