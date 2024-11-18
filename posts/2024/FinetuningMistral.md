---
date: "2024-11-05T11:57:00.00Z"
published: true
slug: FinetuningMistral
tags:
  - RAG
  - Large Language Models
  - AI
  - Finetuning
  - Mistral
time_to_read: 5
title: Finetuning Mistral
description: Best practices for finetuning Mistral
type: post
---

**Mistral Universe**

**Open Source**

1. Mistral 7B
2. Mistral 8\*7 (SMoE)
3. Mistral 8\*22 (Large SMoE)

**Enterprise Grade**

1. small (best for low latency)
2. large - flagship model for most sophisticated needs

**Specialised Model**

1. `Codestral` , low latency model for coding 80+ languages

**Embedding Model**

1. `embed` (transform your data to readable tokens)

Finetuning : https://github.com/mistralai/mistral-finetune

`LoRA` finetuning is used on Finetuning

**Benefits of finetuning**

1. Works significantly better than prompting
2. Typically works better than a larger model , faster and cheaper because it doesn't require a very long prompt
3. provides a better alignment with the task of interest because it has been specifically trained on these tasks
4. Can be used to teach new facts and information to the model such as advanced tools or complicated workflows

**References**

1. https://docs.mistral.ai/getting-started/stories/
2. https://github.com/mistralai/cookbook/blob/main/mistral/fine_tune/mistral_finetune_api.ipynb
3. https://github.com/mistralai/mistral-finetune/blob/main/tutorials/mistral_finetune_7b.ipynb
