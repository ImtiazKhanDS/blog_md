---
date: "2026-10-07T21:21:00.00Z"
published: true
slug: AgenticSearch
tags:
  - Agents
  - AI search
time_to_read: 5
title: Agentic Search
description: Models are good now so a wrong answer usually means the wrong document, not a bad model. Thats the retrieval problem, with two popular fixes
type: post
---

Models are good now so a wrong answer usually means the wrong document, not a bad model. Thats the retrieval problem, with two popular fixes

- **Grep** : Drop the vector database. Give the agent a filesystem and let it list, grep and read files -- the way a developer would
- **Embeddings** : Index everything up front. Embed each chunk, find the closest matches to the question , rerank and hand the top results to the model

#### The strongest case for GREP

- `Anthropic` took RAG out of claude code , They built claude code on RAG, then ripped it out for plain file search - glob, grep , read
- Nothing to keep in sync - the files themselves are the source of truth
- No second copy of your private code sitting in a vector store.
- It looks the way you would : file names, then matching lines, then the whole file.

_The wins - security, privacy, freshness , reliability are what a local filesystem._

But that only worked because its code. Grep did will because code is straightforward: small, already plain text and you know the words to look for.

- Code is small a repo fits on one machine
- Code is tructured symbols, imports , paths
- Code is plain text , you know the words to search
- code is on disk already sitting in a folder

Lose any one of these and grep starts to struggle. Company documents lose all four at once.

#### What company data actually looks like

Theres no folder to grep. Millions of files across thousands of customers - contracts, filings, scanned pdf's , spreadsheets , slide decks. Four problems you dont have with code

- Scale : Its on a server and far too big to list or walk - the search has to run there
- Freshness : It keeps changing - documents get updated and the index cant be answering from an old copy
- Security : Not everyone sees everything - each search has to respect whos allowed to see what
- Format : Its not plain text , theres layout tables, scanned pages and charts.

#### Anthropic's own playbook is already hybrid.

Claude code employs this hybrid model : CLAUDE.md files are dropped into context up front, while glob and grep retrieve files just-in-time

- Its both : CLAUDE.md loaded up front, glob and grep to pull the rest on demand.
- So the win isn't a smarter search -- its the harness : hand the agent every tool and let it choose per question

#### Mostly comes down to scale of data

- 10's - 100's : Just list the files and read them . An index would only get in the way
- 1000's : Grep still works, but it starts missing things when questions are vague. Add semantic search
- millions : Too big to walk . Now you need semantic search just to narrow it down

Same agent, same tools , the right approach shifts as the data grows. So dont lock one in. Let the agent decide.

#### Why not feed everything : More context , bigger bills

- Every token in the window costs GPU memory and compute on every query. Stuffing the whole corpus into the context is the most expensive way to retrieve

- GPU memory - 128K Context ~ 40 GB and grows with every token

- Time to First token > $2*x$ slower for every $2*x$ of context
- And you pay it every query re-sent each request vs an index you build once

_Retrieval flips it : find the handful of chunks that matter and pay for those - not the whole corpus on every call_

**The work around** : Give each subagent its own window, A lead agent fans work out to subagents - each running in its own context window

**The Catch** : Agents typically use about $4*x$ more tokens than chat interactions and multi-agent systems use about $15*x$ more tokens than chats

#### Build a harness , let the agent choose

- Dont pick on method up front . Hand the agent every tool and let it decide hot to look , because neigher works on its own.
- The harness hands it both search broadly , then grep + read to confirm. The agent decides how hard to look , question by question

#### Five tools five questions

1. Where do i even look ? : **retrieval** , hybrid vector + keyword search across the whole index , the broad first pass
2. Whats in this folder ? : **list_files** , Lists files in a directory with paging and name matching
3. Wheres the exact line ? : **grep** Regex search inside files , with the surrounding context
4. What does it actually say ? **read** Reads a file in chunks by offset and tells you how much is left.
5. What does the page look like ? **get_page_screenshot** pulls up the actual page image when layout or a chart matters
