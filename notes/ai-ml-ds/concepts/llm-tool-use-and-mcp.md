# LLM Tool Use, Function Calling, and MCP

## Key Takeaways

- LLMs are text-prediction engines with no built-in ability to call APIs, query databases, or act in the real world -- tool use bridges this gap by letting the model reason about what should happen while the application layer controls execution
- Function calling follows a strict separation: the model outputs structured JSON describing a tool call, the application validates and executes it, and the result feeds back into the model for synthesis (the "agentic loop")
- MCP (Model Context Protocol) reduces integration complexity from N x M (providers x tools) to N + M by standardizing how tools are described, discovered, and invoked across any model
- MCP architecture has three layers: Host (user-facing app), Client (communication handler), and Server (lightweight wrapper around tools/APIs) -- all communicating via a standard protocol
- Tool use introduces real costs: tool definitions consume context window tokens, models can hallucinate function names or malform arguments, and multi-turn loops add latency

## Why LLMs Cannot Act Alone

LLMs have no mechanism to reach beyond their context window. They cannot call an API, query a database, send an email, or perform any action in the real world. They generate text -- nothing more. Tool use is the architectural pattern that gives them the ability to trigger real-world actions.

![LLM tool limitations](../../images/20260531-1210-llm-tool-limitations.png)

## How Function Calling Works

The model and application layer have clearly separated responsibilities:

**Model's role:**
1. Receives the user prompt alongside a menu of available functions (with JSON schema definitions)
2. Decides whether a tool is needed and which one
3. Outputs a structured JSON request -- e.g., `{"function": "get_weather", "arguments": {"location": "Tokyo"}}`
4. Does NOT execute anything

**Application layer's role:**
1. Receives the structured output from the model
2. Validates the function name and arguments
3. Executes the actual function (calling a real API)
4. Returns the result back to the model
5. Can restrict available functions, validate arguments, and require human approval for high-stakes operations

## The Agentic Loop

The agentic loop is the multi-turn cycle that makes complex tool-assisted tasks possible:

1. User sends a prompt
2. Model receives prompt + tool definitions
3. Model generates a structured tool call (JSON, does NOT execute it)
4. Application validates and executes the tool, calling real APIs
5. Result is sent back to the model and added to conversation context
6. Model composes final response -- or determines more tools are needed and loops back to step 3

The loop may repeat multiple times. For example, "find me flights to Tokyo and check the weather there" triggers a flight search call first, then a weather call, with the model synthesizing both results into a final response.

![The agentic loop](../../images/20260531-1211-agentic-loop.png)

The key insight: the LLM only generates text. Actual execution always happens in the application layer.

## Evolution of LLM Tool Integration

![Tool use timeline](../../images/20260531-1212-tool-use-timeline.png)

| Date | Event |
|---|---|
| Mid-2023 | OpenAI formalizes function calling as a first-class API feature |
| Earlier 2023 | ChatGPT Plugins launch -- third-party developers expose arbitrary APIs, but suffer from discovery difficulties, inconsistent quality, and immature security |
| April 2024 | OpenAI deprecates plugins entirely, moves toward controlled function-calling approach |
| Late 2024 | Anthropic introduces MCP as an open-source protocol |
| 2025 | OpenAI announces MCP support; Google DeepMind confirms Gemini support |
| Late 2025 | 10,000+ publicly available MCP servers listed in directories |
| End of 2025 | Anthropic donates MCP to the Agentic AI Foundation (Linux Foundation), co-founded by Anthropic, Block, and OpenAI; supported by AWS, Google, Microsoft, Cloudflare, Bloomberg |

What started as an open-source experiment became an industry standard in roughly one year.

## Model Context Protocol (MCP)

### The N x M Problem

Without a standard protocol, each LLM provider implements function calling differently. With 3 providers and 5 tools, you need 15 custom integrations. Adding a sixth tool means 3 more integrations; adding a fourth provider means 5 more.

MCP reduces this to N + M: each LLM client implements MCP once, each tool server implements MCP once. 3 providers + 5 tools = 8 implementations instead of 15. The savings compound as the ecosystem grows.

![MCP reduces N*M to N+M](../../images/20260531-1213-mcp-nxm-reduction.png)

### Architecture

MCP has three core components:

- **MCP Host** -- the user-facing AI application (e.g., Claude Desktop, AI-powered IDEs)
- **MCP Client** -- lives inside the host, handles communication with external tool providers
- **MCP Server** -- lightweight program that wraps an existing tool, database, or API and exposes it in MCP's standard format

![MCP architecture](../../images/20260531-1214-mcp-architecture.png)

At startup, the client connects to available MCP servers and asks each to describe its capabilities. Those descriptions are fed to the model, triggering the standard function calling mechanism.

### MCP Primitives

MCP servers can surface three types of capabilities:

- **Tools** -- callable functions with defined parameters (the primary driver of adoption)
- **Resources** -- data the model can read (files, database records)
- **Prompt Templates** -- reusable instruction patterns

### Request Flow

![MCP request flow](../../images/20260531-1215-mcp-request-flow.png)

The same tool works with any model that speaks the protocol -- this is MCP's core value proposition.

## Security Risks

### The Postmark Supply Chain Attack (September 2025)

A malicious npm package mimicked a legitimate Postmark email integration for MCP. Hundreds of developers installed it unknowingly. The hidden code silently forwarded copies of every outgoing email to the attacker. This was the first documented supply chain attack targeting MCP servers.

### Broader Concerns

- Every tool exposed to an LLM expands the system's attack surface
- The protocol initially prioritized adoption over robust security; authentication specifications underwent multiple revisions
- Community continues addressing authentication, server identity, and governance gaps
- Pattern follows typical protocol evolution: interoperability and adoption first, security matures with time

Production systems require validation, error handling, approval steps, and sandboxing for untrusted tools.

## Costs and Tradeoffs of Tool Use

**Token consumption:** Tool definitions (names, descriptions, parameter schemas) occupy space in the finite context window. A handful of tools creates negligible overhead, but dozens or hundreds start crowding out the room the model needs to reason. Each additional tool slightly degrades the model's ability to focus.

**Hallucinated calls:** Tool use does not make LLMs deterministic. Models can hallucinate function names, pass malformed arguments, or chain tools in unexpected ways. Validation and human-in-the-loop approval are essential in production.

**Latency:** The multi-turn agentic loop adds latency compared to direct computation, though this tradeoff enables grounding responses in real-time data.

**Core principle:** The model reasons about what should happen, and the application layer controls whether it actually does. That boundary is where security, reliability, and control are designed in.

---

**Source:** https://blog.bytebytego.com/p/connecting-llms-to-the-real-world
**Date:** 2026-05-04
**Tags:** llm, tool-use, function-calling, mcp, agentic-loop, security, model-context-protocol
