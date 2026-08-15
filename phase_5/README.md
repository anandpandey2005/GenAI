
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