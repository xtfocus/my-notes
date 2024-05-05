---
title: "🌐Paper Review: Retrieval-Augmented Generation for Large Language Models: A Survey"
tags: ["paper", "rag"]
---

Update:

Another RAG survey paper came out in April, newer than the paper that I review in this post.
https://arxiv.org/abs/2402.19473


---

[Link](https://arxiv.org/pdf/2312.10997.pdf)

## Naive RAG

has drawbacks:

- Retrieval: precision and recall
- Generation:
    - Hallucination
    - Repetive response due to duplication or repetition in retrieved information if the query is inherently complex, the naive retrieval doesn't provide sufficient context --> placing the burden on to generation
    - Over-relying on the provided context, model simply echoes the info without any contribution to coherence.

---

## Advanced RAG

comes with several hacks:

#### Pre-retrieval
   
**1. [Optimize indexing](#indexing-optimization)**

Strategies include:

- Enhance data granularity
- Optimize index structure
- Add metadata
- Alignment optimization
- Mixed Retrieval

More details

**2. Optimize the original query**

Clarify user's query, make it more suitable for retrieval task. Strategies:

- Rewriting query
- Query expansion
- Others

#### Post-Retrieval

Main methods:

- Rerank chunks: relocate the most relevant content (been implemented in LangChain, LlamaIndex, HayStack)  
- Context compressing: 

>Feeding all relevant documents directly into LLMs can lead
>to information overload, diluting the focus on key details with
>irrelevant content.To mitigate this, post-retrieval efforts con-
>centrate on selecting the **essential information**, emphasizing
>critical sections, and shortening the context to be processed.

---

## Modular RAG

<p align="center">
  <img src="attachments/rags_paradigms.png" alt="rags_paradigms.png"/>
</p>

>Fig. 3. Comparison between the three paradigms of RAG. (Left) Naive RAG mainly consists of three parts: indexing, retrieval and generation. (Middle)
>Advanced RAG proposes multiple optimization strategies around pre-retrieval and post-retrieval, with a process similar to the Naive RAG, still following a
>chain-like structure. (Right) Modular RAG inherits and develops from the previous paradigm, showcasing greater flexibility overall. This is evident in the
>introduction of multiple specific functional modules and the replacement of existing modules. The overall process is not limited to sequential retrieval and
>generation; it includes methods such as iterative and adaptive retrieval.

#### New specialized modules

**1. Search module**

Searching from text chunks is not enough. Need to use structured data sources as well: databases, knowledge graphs, even search engines. 

Example: [KnowledGPT](https://arxiv.org/pdf/2308.11761.pdf) uses graphs.

**2. RAG Fusion**

- generate variations of the original query: this helps expose more angles for retrieval 
- intelligent re-ranking with [Reciprocal Rank Fusion](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=9e698010f9d8fa374e7f49f776af301dd200c548). 

The [author](https://www.linkedin.com/posts/adrian-raudaschl_forget-rag-the-future-is-rag-fusion-activity-7116329392884051968-72tO/#) showed his RFF implementation [here](https://github.com/Raudaschl/rag-fusion/blob/d2c326152c014580e6bd3d0cb059ab424dc4a25f/main.py#L34). The intuition: chunks that are ranked high by many queries will be deemed more relevant/important context.


Critique: 
>However, some answers strayed off topic when the generated queries’ relevance to the original query is insufficient ([Zackary Rackauckas](https://arxiv.org/html/2402.03367v1))

RAG Fusion vs Multi-query: in Multi-query, we take the unique union of all retried docs, while RAG Fusion takes one step further with RRF.

A video by LangChain on how to generate variations [here](https://www.youtube.com/watch?v=JChPi0CRnDY&list=PLfaIDFEXuae2LXbO1_PKyVJiQ23ZztA0x&index=6)

3. Routing

Given a query, decide which pathway is optimal. This is agent-like behavior

(More modules are mentioned in the paper but I found them too abstractive or the cited paper for the module isn't relevant/up-to-date)

#### New Patterns

[HyDE](https://docs.llamaindex.ai/en/stable/examples/query_transformations/HyDEQueryTransformDemo/): generate answers from the original query, then query actual answers based on similarity (interesting idea, but an expensive one)

[Retrieve-Read-Retrieve-Read](https://arxiv.org/abs/2305.15294) is another computationally expensive approach. You can tell what it does from the name and the Figure 3 above.

---

## Indexing Optimization

#### Chunking

Bigger chunks contains more context, but also more noise, more computation time & costs.

Small2Big is an attempt to achieve a balance: retrieve small chunks first, then look around (or zoom out) for more context.

>There are two primary techniques:
>
>-    Smaller Child Chunks Referring to Bigger Parent Chunks: Fetch smaller chunks during retrieval first, then reference the parent IDs, and return the bigger chunks.
>-    Sentence Window Retrieval: Fetch a single sentence during retrieval and return a window of text around the sentence.
>
>([Sophia Yang](https://towardsdatascience.com/advanced-rag-01-small-to-big-retrieval-172181b396d4))

#### Metadata

Page number, file/chapter name, author, etc.

Metadata can also be artificially constructed: generated table/column summaries in [[ingesting_tables_for_rag#level-3-llm-to-generate-the-context|the LlamaIndex Tesla 10-k report example]], for instance.

#### Structural Index

Knowledge graphs or parent-child indexing.

#### Query Optimization

Query Expansion using Multi-query/RAG Fusion have been summarized above.

Query Transformation: Rewrite the query completely or combine with a step-back prompted version to inject high-level concept to user's query.

Query Routing: based on metadata/keywords or semantic routing.

---

## Embedding

#### Hybrid Embedding

While dense vectors are gaining prominence, sparse vectors (created with tfidf, SPLADE, or BM25) can still be useful, especially when the search quality is impacted by **exact keywords**. This results in a Hybrid/Mixed Search. How you actually implement it is up to you, for instance:
- Use sparse vectors for a quick initial search, which narrows the search scope for dense vectors search
- Or use both searches at the same time, then apply fusion (see [this](https://weaviate.io/blog/hybrid-search-explained))

#### Fine-tuning Embedding Model

(Continue-pretraining to be exact) requires expensive + complicated setups.

---

## Fancier Retrieval

This section mentions Iterative, Recursive, and Adaptive Retrieval 

<p align="center">
  <img src="attachments/retrieval_paradigms.png" alt="retrieval_paradigms"/>
</p>

>Fig. 5. In addition to the most common once retrieval, RAG also includes three types of retrieval augmentation processes. (left) Iterative retrieval involves
>alternating between retrieval and generation, allowing for richer and more targeted context from the knowledge base at each step. (Middle) Recursive retrieval
>involves gradually refining the user query and breaking down the problem into sub-problems, then continuously solving complex problems through retrieval
>and generation. (Right) Adaptive retrieval focuses on enabling the RAG system to autonomously determine whether external knowledge retrieval is necessary
>and when to stop retrieval and generation, often utilizing LLM-generated special tokens for control.

---

## What's the paper did not discuss?

On chat history, the paper did mention that

>. In cases of ongoing
>dialogues, any existing conversational history can be integrated
>into the prompt, enabling the model to engage in multi-turn
>dialogue interactions effectively.

and that's it. What I was expecting is:

- techniques to handle ever-expanding dialogues: chat history got too long and the question wasn't even parsed.
- techniques to avoid repetive responses due to greedy similarity search from chat history.

These issues were briefly discussed on [reddit](https://www.reddit.com/r/LangChain/comments/167ndda/conversationalretrievalchains_pigeon_holes_on/), without any breakthrough. However, such problems were [not new](https://www.reddit.com/r/LocalLLaMA/comments/14xyg8h/handling_chat_histories_that_are_longer_than_the/), and a combination of short-term and long-term history sounds reasonable.

LangChain's `ConversationBufferMemory` is also worth looking into.



