
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