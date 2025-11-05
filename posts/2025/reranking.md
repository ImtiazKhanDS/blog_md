---
date: "2025-10-18T21:21:00.00Z"
published: true
slug: Reranking
tags:
  - Agentic AI
  - AI
  - LLM
time_to_read: 5
title: Reranking
description: Reranking is one of the most important topics in semantic search
type: post
---

If you're building semantic search or RAG systems, understanding this architectural difference can make or break your performance.

The Problem: You have 1M documents. A user searches for "python tutorial". How do you find the most relevant results in <150ms?

#### Two Approaches:

###### 🚀 Bi-Encoder (Fast but Less Accurate)

- Encodes query and documents separately
- Pre-computes all document embeddings offline
- At query time: only encode the query (5ms) + vector similarity search (45ms)
- Total: ~50ms for 1M documents ⚡

Limitation: Query and document never "see" each other

###### 🎯 Cross-Encoder (Accurate but Slow)

- Concatenates [query + document] into one input
- Processes them together through transformer
- Models direct interactions via attention mechanism
- The catch: Must encode EVERY (query, doc) pair at query time
- For 1M docs: 1M encodings × 1ms = 1,000 seconds 🐌

**Limitation : Can't pre-compute anything (query-dependent)**

##### Why Cross-Encoder is Slow: The input is [CLS] query [SEP] document [SEP]. When the query changes, ALL encodings must be recomputed. No reusable vectors. No vector database. Every search = O(n) complexity.

###### 💡 The Solution: Two-Stage Pipeline

Stage 1: Bi-encoder retrieves top 100 candidates (50ms) Stage 2: Cross-encoder reranks those 100 (100ms) Total: 150ms with high accuracy ✨

This is how production systems at scale actually work - combining the speed of bi-encoders with the precision of cross-encoders.

###### Key Takeaway:

**Bi-encoders: Great for retrieval (fast, scalable)**

**Cross-encoders: Great for ranking (accurate, expensive)**

Use both: Best of both worlds

If you're building search/RAG systems, don't choose one or the other. Use bi-encoders for candidate retrieval, then cross-encoders for final ranking.

Popular implementations:

**Bi-encoders: sentence-transformers, OpenAI embeddings**

**Cross-encoders: ms-marco models, FlashRank**

Vector DBs: Pinecone, Weaviate, Qdrant

Have you implemented this pattern in production? What's been your experience?
