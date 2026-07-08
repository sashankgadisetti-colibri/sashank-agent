# LangGraph vs Claude Agent SDK for production agents

One-line summary: LangGraph is a model-agnostic, low-level graph orchestration runtime you own end-to-end; Claude Agent SDK is Anthropic's opinionated, batteries-included harness (the Claude Code engine as a library) — pick by how much control vs. speed-to-ship and how model-locked you're willing to be.

## What to read this for
Deciding a framework for a production agent build (e.g., a Colibri internal tool or demo) where you need to weigh build speed against multi-model flexibility, deployment path, and observability.

## Architecture and orchestration model
- **Claude Agent SDK**: Anthropic owns the agent loop (prompt → tool selection → execution → response); you steer via `allowed_tools`, hooks, permissions, and subagents. Claude-only — a hard architectural commitment. [Anthropic docs](https://code.claude.com/docs/en/agent-sdk/overview)
- **LangGraph**: You define a state graph — nodes, edges, conditional routing — and own the loop yourself. Model-agnostic; can route different steps to different models/providers. [LangGraph persistence docs](https://docs.langchain.com/oss/python/langgraph/persistence)
- Secondary sources concur this is the central tradeoff: "opinionated harness" vs. "low-level runtime you own" ([Developers Digest, single-source characterization — could not verify full article, blocked by paywall/403](https://www.developersdigest.tech/blog/claude-agent-sdk-vs-langgraph); [Turion.AI comparison](https://turion.ai/blog/langgraph-vs-openai-claude-agent-sdk-2026/)).

## Tool / MCP integration
- **Claude Agent SDK**: native, first-class MCP support — local process, HTTP, or in-process servers; also ships 20+ built-in tools (Read, Write, Bash, Grep, WebSearch, Agent for subagents). [Anthropic MCP docs](https://platform.claude.com/docs/en/agent-sdk/mcp)
- **LangGraph**: MCP support via the separate `langchain-mcp-adapters` library, which converts MCP tools into LangChain/LangGraph-compatible tools and supports multi-server clients; sessions are stateless by default (fresh MCP session per tool call). [langchain-mcp-adapters GitHub](https://github.com/langchain-ai/langchain-mcp-adapters)
- Practical difference: Claude SDK's MCP is built-in and same-vendor; LangGraph's is a well-maintained but separate adapter layer — one more moving part, though it's what lets LangGraph plug into non-Claude models too.

## State and memory
- **LangGraph**: two-tier by design — checkpointers for short-term, thread-scoped state (conversation continuity, human-in-the-loop pauses, time-travel/replay, fault tolerance; SQLite/Postgres/in-memory backends) and stores for long-term, cross-thread memory (user prefs, facts). [LangGraph persistence docs](https://docs.langchain.com/oss/python/langgraph/persistence)
- **Claude Agent SDK**: sessions with resume/fork; state is JSONL on your filesystem when self-run, or an Anthropic-hosted event log if you move to the separate "Managed Agents" hosted product. Memory/context also draws from `.claude/CLAUDE.md` project files. [Anthropic Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)
- LangGraph's checkpoint/replay model is more purpose-built for long-running, interrupt-heavy workflows (approvals mid-flow); Claude SDK's session model is simpler but less granular for partial-state recovery — this is a vendor-neutral read of both docs, not a documented head-to-head from either vendor.

## Production maturity (deployment, observability, ecosystem)
- **LangGraph**: MIT-licensed, self-host free; paid path is LangSmith (rebranded from "LangGraph Platform" to "LangSmith Deployment," Oct 2025) for tracing, evals, and managed deployment (cloud/hybrid/self-hosted), pricing from ~$39/user/month plus $0.001/node-execution over quota. Cited production users: Klarna, Uber, LinkedIn (vendor/press claim, not independently verified here). [LangSmith deployment](https://www.langchain.com/langsmith/deployment) · [LangGraph Platform GA post](https://www.langchain.com/blog/langgraph-platform-ga)
- **Claude Agent SDK**: runs in your own process/infra; for hosted production without managing sandboxes, Anthropic offers a separate "Managed Agents" REST API product. Subscription plans (from June 15, 2026) added dedicated Agent SDK credits so usage doesn't eat interactive limits — vendor pricing detail, single source. [Anthropic Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)
- Ecosystem breadth favors LangGraph (multi-model, larger third-party integration surface); Anthropic's stack is narrower but tightly integrated with Claude Code tooling you (Sashank) already use daily.

## Where each fits better
- Claude Agent SDK: solo/small-team builds that are fundamentally "Claude working in a repo/filesystem" (coding agents, this very Jarvis setup) — built-in tools and permissions collapse harness work.
- LangGraph: multi-model requirements, long-running/interrupt-heavy workflows needing approvals and replay, or platform teams wanting deployment-provider optionality.

**Recommendation:** For Jarvis and similar Claude-native, filesystem/tool-driven agents, stay on the Claude Agent SDK — reevaluate LangGraph only if a future project needs multi-model routing or heavy human-in-the-loop checkpoint/replay that the SDK's session model doesn't cleanly cover.

## Sources
- [Agent SDK overview — Claude Code Docs (Anthropic, primary)](https://code.claude.com/docs/en/agent-sdk/overview)
- [Connect to external tools with MCP — Claude API Docs (Anthropic, primary)](https://platform.claude.com/docs/en/agent-sdk/mcp)
- [Persistence — Docs by LangChain (primary)](https://docs.langchain.com/oss/python/langgraph/persistence)
- [langchain-mcp-adapters — GitHub (primary)](https://github.com/langchain-ai/langchain-mcp-adapters)
- [LangSmith: Agent Deployment Infrastructure (LangChain, vendor)](https://www.langchain.com/langsmith/deployment)
- [LangGraph Platform is now Generally Available (LangChain blog, vendor)](https://www.langchain.com/blog/langgraph-platform-ga)
- [Comparison with Claude Agent SDK — Docs by LangChain (vendor, could not fully load — 403)](https://docs.langchain.com/oss/python/deepagents/comparison)
- [Claude Agent SDK vs LangGraph: Choosing Your Agent Stack in 2026 — Developers Digest (practitioner, could not fully load — 403, used only via search snippet)](https://www.developersdigest.tech/blog/claude-agent-sdk-vs-langgraph)
- [LangGraph vs OpenAI and Claude Agent SDKs Compared — Turion.AI (practitioner)](https://turion.ai/blog/langgraph-vs-openai-claude-agent-sdk-2026/)

Last updated: 2026-07-08
