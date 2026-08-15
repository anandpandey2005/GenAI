
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