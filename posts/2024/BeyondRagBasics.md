---
date: "2024-10-25T07:57:00.00Z"
published: true
slug: BeyondRAGBasics
tags:
  - RAG
  - Large Language Models
  - AI
time_to_read: 5
title: Beyond RAG Basics
description: Retrieval Augmented Generation is an integral part of semantic search systems.
type: post
---

1. Synthetic Data

   **Action**

   a. Generate synthetic questions for each chunk of text in your database.
   b. Use these to test your retrieval system and calculate precision and recall scores, this is the baseline

   **Reasoning**

   a. helps select right embedding models
   b. enables lightning-fast evaluations
   c. allows rapid iteration and testing of ideas
   d. can be done before you have any real user data.
   e. forces clarity on product goals and non-goals

2. Real-World Data and Clustering

   **Action**

   a. User unsupervised learning to identify question topics and patterns
   b. Work with domain experts to refine and label these clusters
   c. Build few shot classifiers to generate topic distributions for new questions

   **Reasoning**

   a. Types and frequeny of questions per topic
   b. Cosine similarity scores within clusters
   c. Customer satisfaction and feedback per topic
