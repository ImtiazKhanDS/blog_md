---
date: "2025-04-04T21:21:00.00Z"
published: true
slug: MCP
tags:
  - Artificial Intelligence
  - LLMS
  - data science
  - software dev
  - AI Engineering
time_to_read: 5
title: MCP
description: MCP is an open protocol that enables seamless integration between AI apps and agents and your tools and data sources
type: post
---

MCP is an open protocol that enables seamless integration between AI apps and agents and your tools and data sources

**API's**

Standardized how web applications interact with the backend

- servers
- databases
- services

**LSP**

Standardized how IDE's interact with language-specific tools

- Code navigation
- Code analysis
- Code Intelligence

**MCP**

Standardizes how AI applications interact with external systems

- Prompts
- Tools
- Resources

Without MCP : Fragmented AI Development

```mermaid
graph LR

A["AI APP1"] --> B["Custom Implementation"]
B --> C["Custom Prompt Logic"]
C --> D["Custom Tool Calls"]
D --> E["Custom Data Access"]
```

```mermaid
graph LR

A["AI APP2"] --> B["Custom Implementation"]
B --> C["Custom Prompt Logic"]
C --> D["Custom Tool Calls"]
D --> E["Custom Data Access"]
```

```mermaid
graph LR

A["AI APP3"] --> B["Custom Implementation"]
B --> C["Custom Prompt Logic"]
C --> D["Custom Tool Calls"]
D --> E["Custom Data Access"]
```

With the MCP Server

```mermaid

graph LR

A["**Any MCP Client**
  Claude for Desktop
  Cursor
  Goose
  Windsurf
  Your Client
"] --> B["**Database MCP Server**
          Query and fetch data
          Modify Records
          Handle data streams"]

A --> C["**CRM MCP Server**
        Create/Update contacts
        Manage opportunities
        Track Interactions"]

A --> D["**Version Control MCP Server**
        Commit Changes
        Resolve Conflicts
        Manage Branches"]


B --> E["Databases"]
C --> F["CRM Systems"]
D --> G["Version Control Software"]

```

With MCP Standardized AI Development

1. For AI application developers : Connect your app to any MCP Server with 0 additional work
2. For Tool or API Developerfs : Build an MCP server once, see adoption everywhere
3. For end users : More powerful and context-rich AI applications
4. For enterprises : Clear seperation of concerns between AI product teams

MCP growing fast

1. ai applications , from ides to agents
2. server builders 1000+ and counting
3. enterprises for internal and external access
4. open source contributions

**MCP Deep Dive**

```mermaid
graph LR

A["**MCP Client**
     Invokes Tools
     Queries for Resources
     Interpolates Prompts
"] <--> B["**MCP Server**
          Exposes Tools
          Exposes Resources
          Exposes Prompts"]
```

**Tools** : Model-Controlled , Functions invoked by model

1. Retrieve/Search
2. Send a message
3. Update DB records

**Resources** : Application Controlled, Data exposed to the application

1. Files
2. Database Records
3. API Responses

**Prompts**: User-controlled , Pre-defined templates for AI interactions

1. Document Q & A
2. Transcript Summary
3. Output as json

MCP will be the foundational protocal for agents

https://www.anthropic.com/engineering/building-effective-agents

**Building effective agents with mcp**

`Sampling` : Allows a server to request completions from a client , giving the user application full control over security, privacy and cost

```mermaid
graph LR

A["Client"] --> B["Server"]
```

Sampling parameters

1. Model preferences and hints
2. system prompt
3. Temperature and Max Tokens

Client and server is a logical seperation but not a physical one

**Composability**

An MCP client can be a server and vice-versa

```mermaid
graph LR
A["User and LLM"] --> B["Client
                         Server"]
B --> C["Client
         Server"]

C --> D["Client
         Server"]
D --> E["Client
         Server"]
```

**Sampling + Composability**

Chain agents together while making sure that client application controls inference

```mermaid

graph LR

A["Application + LLM"] --> B["Client
                              Server
                              *Agent Orchestrator*"]

B--> C["Client
        Server
        *Analysis Agent*"]

B--> D["Client
        Server
        *Coding Agent*"]

B--> E["Client
        Server
        *Research Agent*"]


```

Whats next for MCP ?

1. MCP Inspector
2. Protocol supports oAuth 2.0 support

**An official MCP Registry API**

A unified, hosted metadata service which enables

1. Discovery
2. Centralization
3. Verification
