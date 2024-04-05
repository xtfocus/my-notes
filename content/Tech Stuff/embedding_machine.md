---
title: Choosing an Embedding Machine For Your RAG
tags: ["rag", "vector-database", "vector-store", "sentence-transformer", "embedding"]
categories: []
---

## Refresher on Embedding

These days, when we talk about embedding for texts, we usually mean *dense* sentence-embedding (versus sparse embedding, which is more like a bag-of-word legacy). Dense vectors capture semantics, which is crucial for similarity search.

>Sounds basic but might come up in interviews. [More about sparse vectors](https://www.pinecone.io/learn/series/nlp/dense-vector-embeddings-nlp/#Dense-vs-Sparse-Vectors)
Earlier, people used BERT for sentence embedding. But BERT was not explicitly built for that: it's for dynamic word-level embedding (token-level, to be exact). 

Then came [Sentence-BERT](https://arxiv.org/abs/1908.10084) (SBERT, 2019), the first specialized transformer for sentences, with a 128-token limit (max sequence length). Since then, many sentence transformers came out pushing this limit.

## Embedding for QnA
Question Answering (QA) is a major NLP application. QA can be categorized into Open-Domain vs Closed-Domain, Extractive vs Open-Generative (Open-book) vs Closed-Generative (Close-book) , depending on the input and output. 

>There are so many ways to categorize them (see [wiki](https://en.wikipedia.org/wiki/Question_answering#Types_of_question_answering) and this [thesis][https://www.lti.cs.cmu.edu/research/dissertations/2023/kaixinm_phd_lti_2023.pdf] and this [survey paper](https://www.iosrjournals.org/iosr-jce/papers/Vol19-issue6/Version-4/D1906041923.pdf))

For convenience sake, we only discuss embedding in Open-Domain Question Answering (ODQA), which is typically used in chat-bot and RAG systems. In such systems, we typically provide a questions as an input, and expect a relevant answer in return.

```
Query: Which bears are the best?
Expected result: Black bears are the best bears.
Actual result: "What kind of bears is the best?" was a question asked in The Office
```

The example above demonstrate the disadvantage of sentence similarity in a QA system: We want aswers, not questions. To overcome this, we need a new model that optimize the distance between questions and their corresponding answers. [Facebook AI’s Dense Passage Retriever](https://arxiv.org/abs/2004.04906) (DPR) was made for this. DPR uses 2 encoders, one for query, one for context (answers). When we pass "Which bears are the best" through each encoder, we get very distance vectors. This makes sure we are less likely to get more questions but more likely to get the answers.

![](https://cdn.sanity.io/images/vr8gru94/production/0bc89c1248e7d6fe44e016f8e1437a7f2bb3fb7d-1920x1020.png)
*(Source [Pinecone](https://www.pinecone.io/learn/series/nlp/dense-vector-embeddings-nlp/#Generating-Dense-Vectors))*


## Summary

Plain sentence-embedding like BERT, SBERT is bad. Use an embedding machine optimized for QA instead. Some candidates:

- DPR (which is a [[Bi-Encoder|bi-encoder]])
- Others: https://huggingface.co/models?pipeline_tag=question-answering&sort=trending
- https://huggingface.co/sentence-transformers/multi-qa-MiniLM-L6-cos-v1
- https://huggingface.co/sentence-transformers/multi-qa-mpnet-base-dot-v1

---
Sources:
- https://www.pinecone.io/learn/series/nlp/dense-vector-embeddings-nlp/#Generating-Dense-Vectors
- https://huggingface.co/transformers/v4.3.3/model_summary.html
- https://huggingface.co/models?pipeline_tag=document-question-answering&sort=trending

