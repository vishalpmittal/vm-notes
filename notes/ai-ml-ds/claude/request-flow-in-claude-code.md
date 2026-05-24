# How Does a Request Actually Travel Through Claude Code

## Key Takeaways

- A user prompt goes through an 8-step pipeline: submit, wrap context, plan, check permissions, dispatch tool, execute, persist state, stream response
- The core is a Plan-Act-Observe agent loop that repeats until the task is complete
- Every tool action passes through a permission gate (Allow/Ask/Deny) before execution

![What Happens When You Hit Enter in Claude Code](../images/20260523-1730-claude-code-request-flow.jpeg)

## The 8-Step Request Flow

1. **User Submission** — User sends a prompt (e.g., "Fix the failing test in auth.test.ts") via CLI, IDE, or SDK.

2. **Context Wrapping** — The interface wraps the prompt with repo and file context and hands it to the agent loop as a request.

3. **Agent Planning** — The agent loop analyzes the task and proposes an action (e.g., edit auth.ts lines 42-55).

4. **Permission Verification** — The permission system checks the proposed action against rules (Allow / Ask / Deny).

5. **Tool Dispatch** — The approved action becomes a tool call sent to the appropriate tool (Read / Edit / Bash).

6. **Execution** — The tool runs in the execution environment (shell, cloud, or sandbox) as a real syscall. Returns result (e.g., `{ ok: true, diff: +5/-3 }`).

7. **State Persistence** — The agent persists the turn to state and updates memory.

8. **Streaming Response** — The agent streams the final message to the user.

The system repeats the Plan-Act-Observe loop iteratively until the model completes the task and stops requesting tools.

---

**Source:** https://blog.bytebytego.com/i/198874402/how-does-a-request-actually-travel-through-claude-code
**Date:** 2026-05-23
**Tags:** claude-code, architecture, agent-loop
