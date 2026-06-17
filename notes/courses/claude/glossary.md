# Glossary

| Term | Definition | Reference |
|---|---|---|
| 4D Framework | Four competencies for AI fluency: Delegation, Description, Discernment, Diligence | [Claude 101](claude-101.md#4d-framework-for-ai-fluency) |
| Agentic Loop | The repeating cycle Claude Code runs: gather context → take action → verify results → loop until done | [Claude Code 101](claude-code-101.md#the-agentic-loop) |
| AI Agent | Software that interacts with its environment and takes actions to complete a defined goal | [Claude Code 101](claude-code-101.md#what-an-ai-agent-is) |
| Approval Mode | Default permission mode — Claude asks before each file edit and command | [Claude Code 101](claude-code-101.md#3-permission-modes--plan-mode) |
| Auto-Accept Mode | Permission mode where file edits are auto-approved but commands still need approval | [Claude Code 101](claude-code-101.md#3-permission-modes--plan-mode) |
| CLAUDE.md | Persistent project memory file Claude Code reads at the start of every session | [Claude Code 101](claude-code-101.md#7-claudemd--persistent-project-memory) |
| Compaction | Auto-summarization of conversation when context window fills up; can lose detail | [Claude Code 101](claude-code-101.md#mental-model) |
| Connectors | Integrations that give Claude access to external tools, data, and context via MCP | [Claude 101](claude-101.md#connectors) |
| Constitutional AI | Training method where AI self-critiques against a set of principles to align with human values | [Claude 101](claude-101.md#key-takeaways) |
| Context Window | Claude's finite working memory — holds prompts, file reads, tool calls, and results | [Claude Code 101](claude-code-101.md#mental-model) |
| Delegation | Deciding what work humans vs. AI should do and distributing tasks strategically | [Claude 101](claude-101.md#4d-framework-for-ai-fluency) |
| Description | Effectively communicating with AI — defining outputs, guiding processes, specifying behaviors | [Claude 101](claude-101.md#4d-framework-for-ai-fluency) |
| Desktop Extensions | Connectors that run locally via Claude Desktop app for access to local files and native apps | [Claude 101](claude-101.md#connectors) |
| Diligence | Using AI responsibly — transparency, ethical choices, accountability for AI-assisted work | [Claude 101](claude-101.md#4d-framework-for-ai-fluency) |
| Discernment | Critically evaluating AI outputs for quality, accuracy, and appropriateness | [Claude 101](claude-101.md#4d-framework-for-ai-fluency) |
| Explore → Plan → Code → Commit | The recommended Claude Code workflow — always explore and plan before writing code | [Claude Code 101](claude-code-101.md#4-daily-workflow-explore--plan--code--commit) |
| Hooks | Deterministic commands that always run at specific Claude Code lifecycle events | [Claude Code 101](claude-code-101.md#11-hooks) |
| HTTP MCP Server | Remote MCP server hosted by a service provider, connected over the network | [Claude Code 101](claude-code-101.md#adding-servers) |
| MCP | Model Context Protocol — open standard connecting Claude to external apps (like USB-C for AI) | [Claude Code 101](claude-code-101.md#10-mcp-model-context-protocol) |
| Plan Mode | Read-only mode that lets Claude analyze and propose a plan without writing any files | [Claude Code 101](claude-code-101.md#3-permission-modes--plan-mode) |
| PostToolUse | Hook event firing after a tool call completes — common for auto-formatters | [Claude Code 101](claude-code-101.md#events) |
| PreToolUse | Hook event firing before a tool call — can block execution via exit code 2 | [Claude Code 101](claude-code-101.md#blocking-with-pretooluse) |
| Projects | Knowledge hubs that store reference materials Claude draws on across all chats in a project | [Claude 101](claude-101.md#skills-vs-projects) |
| Skills | Folders of instructions, scripts, and resources Claude loads dynamically; leaner than MCP because lazy-loaded | [Claude Code 101](claude-code-101.md#9-skills) |
| Stdio MCP Server | MCP server that runs as a local process on your machine | [Claude Code 101](claude-code-101.md#adding-servers) |
| Subagent | Isolated agent with its own context window that handles a task and returns just a summary | [Claude Code 101](claude-code-101.md#8-subagents) |
| Tool Search Mode | Auto-activated when MCP tools exceed 10% of context — discovers tools on demand (less reliable) | [Claude Code 101](claude-code-101.md#context-cost-important) |
| Web Connectors | Connectors that link Claude to cloud services like Google Drive, Notion, Slack, and Asana | [Claude 101](claude-101.md#connectors) |
| `--from-pr` | CLI flag to resume the session originally linked to a PR | [Claude Code 101](claude-code-101.md#6-code-review-features) |
| `/clear` | Wipes the conversation for a completely fresh session | [Claude Code 101](claude-code-101.md#commands) |
| `/commit-push-pr` | Skill that handles commit + push + PR creation in one step | [Claude Code 101](claude-code-101.md#6-code-review-features) |
| `/compact` | Manually triggers conversation compaction to free up context space | [Claude Code 101](claude-code-101.md#commands) |
| `/context` | Shows context size breakdown with categories and a visual bar chart | [Claude Code 101](claude-code-101.md#commands) |
