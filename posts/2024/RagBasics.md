---
date: "2024-24-10T11:57:00.00Z"
published: true
slug: RAGBasics
tags:
  - RAG
  - Large Language Models
  - AI
time_to_read: 5
title: RAG Basics
description: Retrieval Augmented Generation is an integral part of semantic search systems.
type: post
---

Retrieval Augmented Generation : LLM + AI Vectors

R in RAG is the Retrieval part : The process of obtaining relevant information
based on a users information need expressed as a query/question.

How to Evaluate Information Retrieval Systems ?

You get retrieved documents from a query then :

1. Judge query document pairs
2. Binary Judgement (not relevant, relevant)
3. Graded Judgement ( not relevant, relevant , slightly relevant , very relevant)

Common IR Datasets

1. Text REtrieval Conference (TREC) - Different Domains and spanning decades
2. MS Marco Passage/Document Ranking - Web search domain from Bing
3. BEIR Benchmark (beir.ai) : Many different domains and tasks , collection of IR collections

Ranking Metrics

1. `Recall@K` : Fraction of relevant entities from all relevant entities.
2. `Precision@K` : Fraction of relevant entities from all retrieved entities
3. `NDCG@K` :
   a. https://www.evidentlyai.com/ranking-metrics/ndcg-metric
   b. https://towardsdatascience.com/demystifying-ndcg-bee3be58cfe0

Lets explain NDCG in detail here:

<table>
    <thead>
        <tr>
            <th>search_group_id</th>
            <th>item_id</th>
            <th>Ranks</th>
            <th>Gains</th>
        </tr>
    </thead>
    <tbody>
        <tr style="color: blue;">
            <td>x</td>
            <td>item_a</td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr style="color: blue;">
            <td>x</td>
            <td>item_b</td>
            <td>2</td>
            <td>0</td>
        </tr>
        <tr style="color: blue;">
            <td>x</td>
            <td>item_c</td>
            <td>3</td>
            <td>1</td>
        </tr>
        <tr style="color: blue;">
            <td>x</td>
            <td>item_d</td>
            <td>4</td>
            <td>1</td>
        </tr>
        <tr style="color: blue;">
            <td>x</td>
            <td>item_e</td>
            <td>5</td>
            <td>1</td>
        </tr>
        <tr style="color: red;">
            <td>y</td>
            <td>item_f</td>
            <td>1</td>
            <td>1</td>
        </tr>
        <tr style="color: red;">
            <td>y</td>
            <td>item_g</td>
            <td>2</td>
            <td>0</td>
        </tr>
        <tr style="color: red;">
            <td>y</td>
            <td>item_h</td>
            <td>3</td>
            <td>1</td>
        </tr>
        <tr style="color: red;">
            <td>y</td>
            <td>item_i</td>
            <td>4</td>
            <td>0</td>
        </tr>
        <tr style="color: red;">
            <td>y</td>
            <td>item_j</td>
            <td>5</td>
            <td>1</td>
        </tr>
    </tbody>
</table>

There are two search queries `x` and `y` , Within each group there are five different items as shown.
The last column is the gains for each item representing relevance of each item within the search.

Cumulative Gain : $CG = \sum_{i=1}^{\text{ranks}} \text{Gains}_i$

Calculating CG for two groups

$CG_{x} = 0+0+1+1+1 = 3$
$CG_{y} = 1+0+1+0+1 = 3$

This is the drawback of cumulative gain it doesnt take rank into consideration. Now thats we have one more thing called Discounted Cumulative Gain which takes rank into consideration.

$DCG = \sum_{i=1}^{\text{ranks}} \frac {\text{Gains}_i}{\log_{2}({i+1})}$

$DCG_{x} = \frac{0}{\log_{2}(1+1)} + \frac{0}{\log_{2}(2+1)} + \frac{1}{\log_{2}(3+1)} + \frac{1}{\log_{2}(4+1)} + \frac{1}{\log_{2}(5+1)}
= 1.31752$

$DCG_{y} = \frac{1}{\log_{2}(1+1)} + \frac{0}{\log_{2}(2+1)} + \frac{1}{\log_{2}(3+1)} + \frac{0}{\log_{2}(4+1)} + \frac{1}{\log_{2}(5+1)}
= 1.88685$

Now it makes sense since now group y is better since most of the ranks are taken into consideration.

Now why do we need NDCG then, lets introduce one more group to understand that.

<table>
    <thead>
        <tr>
            <th>search_group_id</th>
            <th>item_id</th>
            <th>Ranks</th>
            <th>Gains</th>
        </tr>
    </thead>
    <tbody>
        <tr style="color: green;">
            <td>z</td>
            <td>item_k</td>
            <td>1</td>
            <td>1</td>
        </tr>
        <tr style="color: green;">
            <td>z</td>
            <td>item_l</td>
            <td>2</td>
            <td>0</td>
        </tr>
        <tr style="color:green;">
            <td>z</td>
            <td>item_m</td>
            <td>3</td>
            <td>0</td>
        </tr>
        <tr style="color: green;">
            <td>z</td>
            <td>item_n</td>
            <td>4</td>
            <td>0</td>
        </tr>
        <tr style="color:green;">
            <td>z</td>
            <td>item_o</td>
            <td>5</td>
            <td>0</td>
        </tr>
    </tbody>
</table>

$DCG_{z} = \frac{1}{\log_{2}(1+1)} + \frac{0}{\log_{2}(2+1)} + \frac{0}{\log_{2}(3+1)} + \frac{0}{\log_{2}(4+1)} + \frac{0}{\log_{2}(5+1)}
= 1$

The DCG of `z` is 1. but it has the most relevant item at the first item at the first rank.
If we compare the data it should be atleast better than `x`. The problem is group x has three relevant items and group z only has one , its not fair to just compare the DCG since it's cumulative sum, that is why we need NDCG.

IDCG stands for ideal discounted cumulative gain, which is calculating the DCG of the ideal order based on the gains. It answers the question: what is the best possible DCG for a group? Returning to a real-world example, when a user searches for something online they always want to have the most relevant item at the top and above any irrelevant items. That is, all the relevant information should always be at the top, and it should have the best DCG.

$IDCG_{x} = \frac{1}{\log_{2}(1+1)} + \frac{1}{\log_{2}(2+1)} + \frac{1}{\log_{2}(3+1)} + \frac{0}{\log_{2}(4+1)} + \frac{0}{\log_{2}(5+1)}
= 2.13093$

$IDCG_{y} = \frac{1}{\log_{2}(1+1)} + \frac{1}{\log_{2}(2+1)} + \frac{1}{\log_{2}(3+1)} + \frac{0}{\log_{2}(4+1)} + \frac{0}{\log_{2}(5+1)}
= 2.13093$

$IDCG_{z} = \frac{1}{\log_{2}(1+1)} + \frac{0}{\log_{2}(2+1)} + \frac{0}{\log_{2}(3+1)} + \frac{0}{\log_{2}(4+1)} + \frac{0}{\log_{2}(5+1)}
= 1$

$NDCG = \frac{DCG}{IDCG}$

$NDCG_{x} = \frac{1.31752}{2.13093} = 0.61828$

$NDCG_{y} = \frac{1.88685}{2.13093} = 0.88546$

$NDCG_{z} = \frac{1}{1} = 1$

What Is NDCG@K?
K means the top K ranked item of the list, and only top K relevance contributes to the final calculation. When we are calculating the NDCG@K, we first calculate the DCG up to K items from the actual relevance order and ideal relevance order, then get the normalized DCG of that result.

Here is the formula for NDCG@K:

$NDCG@K = \frac{\sum_{i=1}^{k (actual order)} \frac{Gains_i}{\log_{2}(i+1)}}{\sum_{i=1}^{k (ideal order)} \frac{Gains^{*}_i}{\log_{2}(i+1)}}
$

4. `Reciprocal Rank` : https://www.evidentlyai.com/ranking-metrics/mean-reciprocal-rank-mrr

$RR = \frac{1}{rank \; of \; the \; first \; correct \; result}$

MRR (mean reciprocal rank): MRR is a measure of the rank of the first relevant item in a ranked list. It is calculated by taking the reciprocal of the rank of the first relevant item, and averaging this value across all queries or users. For example, if the first relevant item for a given query has a rank of 3, the MRR for that query would be 1/3. MRR ranges from 0 to 1, with higher values indicating better performance.

$MRR = \frac{1}{U} \sum_{u=1}^{U} \frac{1}{rank_i}$

5. `LGTM@10` : Looks good to me, manual evaluation

Building your own IR Dataset which is better in so many ways

1. If you have real traffic , sample it
2. No traffic, use llm to generate queries for your content
3. Preferably static collection documents.

Using sample prompt for relevance judgement
LLM as an relevance assessor.
https://arxiv.org/pdf/2406.06519

with your own eval dataset you can basically track the changes. For example

Lets say you added title to your dataset and created vectors. Now you can measure the NDCG@K to see the percentage increase or decrease in that metric.

Insights

1. Dense representations beyond 256 tokens are bad for high precisionn search
2. You need to chunk for meaningful vector representations for search
3. IR is more than single vector representation
4. Build your own evals
5. Dont ignore the BM25 baseline
6. Hybrid capabilities avoids the worst failure modes
7. Long context single-vector embedding models underperforms
8. Real-world search is more than text similarity

Thank you Jo Bergum from vespa ai @jobergum

References

1. https://parlance-labs.com/education/rag/jo.html
