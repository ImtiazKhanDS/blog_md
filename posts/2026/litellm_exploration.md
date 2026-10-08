---
date: "2026-10-09T21:21:00.00Z"
published: true
slug: LiteLLM
tags:
  - Agents
  - AI Harness
  - LiteLLM
time_to_read: 5
title: LiteLLM
description: LiteLLM is an open source LLM gateway that puts one OpenAI compatible API in front of 100+ model providers. This video covers what it is, the two ways to run it, and how the complexity auto router sends each request to a model tier you define so simple work stops hitting your most expensive model.
type: post
---

LiteLLM flavors

1. SDK

   - In your python applicaton
   - One completion() call
   - swap one string to change provider
   - 100+ providers

2. Gateway

   - Generate virtual keys
   - Track usage and spend
   - see every model available
   - Guardrails and more

3. Enterprise

   - Add and manage team members
   - SSO past five users
   - Audit logs and prometheus
   - Monitor usage at org scale

### AutoRouter

Most AI apps let you pick one model, and then every query goes to it. "What's the capital of France?" and "refactor the auth module" end up on the same frontier model, at the same price.

LiteLLM Auto Router picks the model per request. Simple prompts go to a cheap model and complex ones go to a frontier model, all behind one model name in your config.yaml.

- You can define four tiers—SIMPLE, MEDIUM, COMPLEX, and REASONING—and map them to specific models (e.g., GPT-5.6 Luna for simple tasks and Claude-Opus-5 for complex ones).
- Keyword Routing: The router can be configured to force specific prompts to higher-tier models if they contain sensitive keywords like "security review" or "incident."
- LLM Classifier : For more advanced needs, you can integrate an LLM classifier that analyzes prompts after initial heuristic checks to decide on the best routing path, using previous conversation turns as context.- A default model, an escalation keyword, and housekeeping calls like session renaming routed to the cheapest tier
- Housekeeping & Defaults : You can set default models and rules for routine tasks (like session renaming) to ensure they are always handled by the most cost-effective tier.

### Unified MCP Gateway

- Litellm provides a single URL to connect all MCP Clients to your entire catalog of MCP Servers.
- Enterprise authentication involves two authentication legs: client-to-litellm via SSO and litellm-to-Mcp-server using either OAuth2 with PKCE (for users) or M2M/client credentials for agents
- Resource controls : You can define teams either manually or by syncing with an identity provider (IdP) via SCIM and assign specific MCP servers and granular tool permissions to each team.
- Advanced Governance :
  - Access Groups : Group multiple MCP servers together for easier distribution to teams
  - Toolsets : Create collection of tools spanning different mcp servers to assign to specific teams
- Budgeting and Monitoring : Organizations can set team-wide budgets with soft alerts or govern costs at the individual tool-call level to monitor spend per query across different servers.

### Agent Gateway

Bring agents from any platform into the LiteLLM gateway and manage them in one place.

- Agent Registration: You can add agents using the A2A Standard protocol or platforms like LangGraph, Azure AI Foundry, Bedrock AgentCore, and Vertex AI
- Gateway Configuration: When registering an agent, you provide the agent's URL, which helps the gateway automatically fill in metadata, descriptions, and protocol versions
- Governance and Control: The gateway allows you to assign specific skills, set costs (per query or token), define allowed models, and manage sub-agents or MCP servers. It also provides governance features like session budgets, rate limits, and guardrails
- Activation: Newly registered agents often start with a "Needs Setup" status; they become active once you assign a virtual key through the gateway's virtual key section
- Configuration Files: You can also declare and manage agents directly via a config.yaml file, which allows for easier deployment and organization
- Monitoring: The gateway provides a centralized hub to test agents, check logs, and monitor usage statistics, simplifying agent management
