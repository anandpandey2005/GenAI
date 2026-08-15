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