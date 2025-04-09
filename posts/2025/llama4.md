---
date: "2025-04-09T21:21:00.00Z"
published: true
slug: llama4
tags:
  - Artificial Intelligence
  - LLMS
time_to_read: 5
title: llama4
description: llama 4 models include Behemoth(288B active params 16 experts and 2T total params), maverick(17B, 128 experts, 400B total), scout(17B active parameters, 16 experts, 109B total)
type: post
---

**Llama 4 model**

- Behemoth(288B active params 16 experts and 2T total params)
- maverick(17B active params, 128 experts, 400B total params) supports 1M context
- scout(17B active params, 16 experts, 109B total params) supports 10M context

**Insights from Pretraining**

- Llama shifted to mixture of experts architecture, In MOE models , a single token activates only a fraction of the total parameters
- Designed with native multimodality using early fusion , major step since it enables jointly pre-train the model with large amounts of
  unlabeled text , image and video data
- New training technique `MetaP` that allows to reliably set critical model hyper paramters such as per layer learning rates and initialization scales
- Efficient training by using fp8 precision

**Insights from Posttraining**

- Supervised finetuning , followed by online reinforcement learning , followed by lightweight direct preference optimization.
- Observation is that SFT and DPO can over constrain the model , restricting exploration , so they significantly reduced that.

**Other Insights**

- Llama 4 scout might be used for multi-document summarization, parsing extensive user activity for personalized tasks, and reasoning over vast code bases
- Llama Guard: Our input/output safety large language model based on the hazards taxonomy we developed with MLCommons. Developers can use it to detect whether inputs or outputs violate the policies they’ve created for their specific application.
- Prompt Guard: A classifier model trained on a large corpus of attacks, which is capable of detecting both explicitly malicious prompts (Jailbreaks) as well as prompts that contain inject inputs (Prompt Injections).
- CyberSecEval: Evaluations that help AI model and product developers understand and reduce generative AI cybersecurity risk.
