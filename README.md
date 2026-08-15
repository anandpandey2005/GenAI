# The Complete GenAI / LLM Engineer Roadmap — Master Notes (2026)

One path. Every phase = concepts → subtopics to actually master → interview questions asked from it → a project that proves you know it. Do the phases in order — each one is load-bearing for the next.

---

## Phase 0 — Foundations

**Why this phase exists:** every later phase assumes this. Skipping it is the #1 reason people fumble easy questions.

**Concepts & subtopics to master:**
- Python for AI systems: `async`/`await`, generators, `dataclasses`, Pydantic v2 models, type hints, context managers
- Tokenization: BPE, WordPiece, SentencePiece — why token count ≠ word count, how it drives cost
- Core math intuition (not proofs): dot product = similarity, cosine similarity, softmax, gradient descent
- Classical ML vocabulary you'll keep needing: overfitting/underfitting, train/val/test split, precision/recall/F1, confusion matrix, bias-variance trade-off
- Neural net basics: embedding layers, forward/backward pass intuition
- Why Transformers replaced RNNs: parallelism + long-range dependency handling

**Interview questions this earns you:**
- "What is a token, and why does an API charge per token?"
- "Explain overfitting in plain English."
- "Why can Transformers process a sequence in parallel but an RNN can't?"

**Project:** none needed — this phase is "be fluent," not "build."

---

## Phase 1 — Core LLM Mechanics, Tokenomics & Structured Outputs

**Concepts & subtopics to master:**
- Transformer core: self-attention math $\text{Softmax}(\frac{QK^T}{\sqrt{d_k}})V$, multi-head attention, RoPE (Rotary Position Embeddings), KV-caching mechanics, context window scaling
- Decoding & sampling: temperature vs Top-P vs Top-K, Min-P sampling, greedy vs beam search, logit bias, repetition penalty
- Structured output enforcement: JSON mode vs tool calling vs constrained/grammar-based decoding (Outlines / Instructor / Pydantic)
- Reasoning models: Chain-of-Thought (CoT), internal reflection/thinking-token budgets, cost vs latency trade-offs
- Context engineering: managing the *whole* context window (system prompt + retrieved docs + tool outputs + memory) inside a token budget — the term that has replaced "prompt engineering" in job specs
- Prompt caching: cutting cost/latency on repeated system prompts
- Long-context vs RAG trade-off: with 1M+ token windows now common, know exactly when you still need retrieval (cost, freshness, multi-tenant isolation) vs just stuffing context
- Model routing: cheap queries → small model, hard queries → frontier model, as a cost-control pattern

**Interview questions this earns you:**
- "How does KV-cache reduce inference time from O(N²) toward O(N) during autoregressive generation?"
- "Why doesn't low temperature guarantee valid JSON without schema enforcement?"
- "When would you keep using RAG even with a 1M-token context window?"

**Project — High-Throughput Async Invoice & Contract Ingestion Engine:**
Build an asynchronous document parsing pipeline in Python (`asyncio` + Pydantic v2 + schema-constrained LLM tool calls). Ingest messy PDFs/receipts, stream tokens to the client via Server-Sent Events (SSE), auto-retry failed schema validations with a self-correcting prompt loop, store validated records in PostgreSQL.

---

## Phase 2 — Production-Grade RAG & Hybrid Retrieval

**Concepts & subtopics to master:**
- Chunking strategies: semantic boundary chunking, hierarchical (parent-child) chunking, Markdown/AST-aware parsing
- Hybrid search & fusion: dense embeddings (Sentence-Transformers) + sparse lexical (BM25/SPLADE), fused with Reciprocal Rank Fusion (RRF)
- Re-ranking: cross-encoder rerankers (Cohere, BGE-Reranker) vs ColBERT-style late-interaction/multi-vector scoring
- Query transformation & routing: HyDE (Hypothetical Document Embeddings), Step-Back prompting, multi-query expansion, semantic query routing
- GraphRAG: extracting entities + relations into a knowledge graph (Neo4j) to solve multi-hop reasoning where pure vector search fails
- Retrieval failure taxonomy — be able to name each failure mode precisely: lexical gap, semantic drift, chunk-boundary loss, stale index, multi-hop failure
- Semantic caching (GPTCache-style): cache by embedding similarity, not exact string match
- Agentic RAG: retrieval as a re-callable tool an agent invokes repeatedly, not a single fixed step

**Interview questions this earns you:**
- "When does vector similarity search fail, and how does hybrid search (BM25 + dense + RRF) fix it?"
- "Bi-encoder vs cross-encoder — trade-off in latency vs accuracy?"
- "Why is vector search bad for exact matches like part numbers or emails?"

**Project — Multi-Hop Financial & Legal Graph-RAG System:**
Ingest 100+ pages of dense financial filings (10-Ks) with tables and cross-sectional data. Combine pgvector/Qdrant (dense + BM25 hybrid) with Neo4j knowledge-graph extraction. Add a cross-encoder reranking step and an AST-based Markdown table parser that can accurately answer balance-sheet queries.

---

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

## Phase 4 — Local Model Serving, Quantization, Fine-Tuning & Alignment

**Concepts & subtopics to master:**
- High-throughput inference engines: vLLM and SGLang — PagedAttention, continuous batching, chunked prefill, speculative decoding
- Quantization: BitsAndBytes, GPTQ, AWQ, FP8 precision trade-offs
- PEFT (Parameter-Efficient Fine-Tuning): LoRA, QLoRA, rank (r), alpha (α), target modules
- Model alignment: SFT vs DPO (Direct Preference Optimization) vs GRPO (Group Relative Policy Optimization) — and *why* DPO largely replaced RLHF (no separate reward model, more stable training)
- The escalation ladder interviewers want to hear, in order, each step justified by cost: prompt/context engineering → RAG → fine-tuning. Freshers and juniors most commonly lose points by recommending fine-tuning too early.
- Small Language Models (SLMs) for on-device/edge deployment (Phi, Gemma, Qwen-small) — latency/cost/privacy trade-off vs calling a frontier API
- GRPO context: the RL method behind reasoning-model training (DeepSeek-R1 lineage) — know this if you claim "reasoning models" as a skill

**Interview questions this earns you:**
- "How does PagedAttention solve memory fragmentation during multi-user concurrent generation?"
- "Why has DPO largely superseded RLHF in standard fine-tuning pipelines?"
- "When would you fine-tune instead of using RAG — and how do you justify that cost to a PM?"

**Project — Domain-Adapted Medical/Legal SLM with Self-Hosted Inference:**
Fine-tune an open-weight model (Llama-3-8B or Qwen-2.5-7B) on a specialized dataset format. Use Unsloth / Hugging Face `trl` to run QLoRA SFT on structured synthetic data, export the merged model, and serve it on a containerized vLLM instance with custom continuous batching and Prometheus metrics.

---

## Phase 5 — LLMOps, Rigorous Evaluation & GenAI System Design

**Concepts & subtopics to master:**
- Automated evaluation harnesses: Ragas & DeepEval (faithfulness, context relevance, answer semantic similarity, hallucination detection)
- Online vs offline evaluation, explicitly: offline = fixed benchmark before deploy (fast, reproducible); online = sampled production traffic scored via LLM-as-judge (reflects real behavior, slower to iterate). Best practice = maintain both.
- Observability & tracing: OpenTelemetry, Langfuse / Arize Phoenix — tracing tokens, latency per node, cost dashboards
- **AI Security — OWASP Top 10 for LLM Applications** (increasingly its own interview round, not a subtopic): direct/indirect prompt injection, insecure output handling, training-data poisoning, excessive agency, sensitive information disclosure, PII masking, NeMo Guardrails
- System design for GenAI: multi-tier caching (exact-match Redis + semantic embedding cache via GPTCache), rate-limiting, fallbacks/fail-overs
- The canonical system design prompt to have a rehearsed answer for: *"Design an enterprise AI support system serving 100,000 queries/min, $0.005 budget per query, sub-second TTFT (time-to-first-token)."*
- CI/CD for eval: a pipeline that blocks a PR if hallucination score exceeds a threshold

**Interview questions this earns you:**
- "Design an enterprise AI customer-support system at 100k queries/min under a $0.005/query budget with sub-second TTFT."
- "How do you build a CI/CD eval pipeline that blocks PRs if hallucination score exceeds 5%?"
- "Walk me through your defenses against indirect prompt injection from retrieved content."

**Project — Enterprise Observability, Guardrail & Eval Pipeline:**
Build an API gateway sitting in front of multiple LLMs with: semantic caching (Redis), input/output guardrails for toxicity and prompt injection, OpenTelemetry tracing into Langfuse, and an automated GitHub Actions CI pipeline running Ragas test suites against a synthetic golden dataset.

---

## Phase 6 — Multimodal & Applied Generation

**Concepts & subtopics to master:**
- Diffusion models at a conceptual level: denoising steps, latent space — enough to reason about trade-offs, not to train one from scratch
- Multimodal RAG: retrieving across text + image + table in a single pipeline
- Speech pipelines: STT/TTS basics if the role touches voice agents (increasingly common in Indian startup JDs)
- Output safety design pattern for generative content: input filtering → output classifiers → human review gate on risky content → a fast kill-switch for a bad generation already served

**Interview questions this earns you:**
- "A user tries to generate something offensive or a fake of a real person through your image feature — what stops them?"
- "Where did the training data come from, and can we copyright what this model produces?" (know the shape of the issue, not legal specifics)

**Project:** extend the Phase 5 gateway to accept an image-generation endpoint and add the output-safety layer above — reuses your existing guardrail infrastructure instead of starting a new stack.

---

## How it all locks together

Each phase's project is designed to plug into the next:
Phase 1 (structured extraction) → feeds documents into Phase 2 (RAG) → Phase 2's retriever becomes a tool inside Phase 3's agent → Phase 3's agent gets served efficiently via Phase 4 → Phase 5 wraps everything in eval/observability/guardrails → Phase 6 extends the same gateway to multimodal.

If you want one single capstone instead of six separate builds: take the Phase 3 agent swarm, point it at your own product's docs using the Phase 2 retriever, serve it via Phase 4's vLLM setup, and wrap it in the Phase 5 gateway. That one system demonstrates all six phases end-to-end and is the strongest thing you can walk an interviewer through.