
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