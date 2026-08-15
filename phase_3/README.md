
## Phase 3 — Stateful Multi-Agent Workflows & Protocol Integration

**Concepts & subtopics to master:**
- Agent patterns: ReAct loops, reflection/self-correction, plan-and-solve, human-in-the-loop approval
- State machines & graph execution: LangGraph `StateGraph` — cycles, conditional branching, checkpointing, time-travel debugging
- **Current stack (know this exactly, it's tested directly):** `AgentExecutor` is deprecated (maintenance-only). Use `create_react_agent` for simple prebuilt tool-using agents, or a hand-built `StateGraph` for anything needing cycles/branching/human-in-the-loop. LangChain now runs *on top of* the LangGraph runtime — they aren't competing frameworks anymore.
- Threads & persistent checkpoints: an agent that pauses for human approval and resumes days later with full state intact
- Tool calling protocols: Model Context Protocol (MCP) — client/server roles, tool discovery, how it differs from a plain function-calling schema
- Agent memory hierarchies: working context vs short-term session memory vs long-term episodic/semantic memory (Zep/Mem0)
- Deadlock/loop prevention: max-iteration guards, per-run cost budgets, cycle detection in graph execution

**Interview questions this earns you:**
- "When should you avoid a multi-agent framework in favor of a deterministic DAG or single LLM chain?"
- "How do you handle cyclic loops, deadlocks, and cascading error propagation in an agent swarm?"
- "Design an agent that pauses for 2 days awaiting human approval and resumes reasoning correctly."

**Project — Autonomous AI Code Reviewer & Bug-Fixing Swarm:**
Build an end-to-end multi-agent coding engine on LangGraph (1.0 API, not `AgentExecutor`):
- **Router Agent** — parses the incoming GitHub PR webhook
- **Linter/Executor Agent** — runs tests in an isolated sandbox
- **Reviewer Agent** — flags security vulnerabilities and syntax bugs
- **Fixer Agent** — rewrites code, re-runs tests, auto-iterates until green, opens a ready-to-merge PR
- Wire in time-travel checkpoints so any step can be replayed/inspected

---