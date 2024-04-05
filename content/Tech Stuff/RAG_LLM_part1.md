---
title: "Build a RAG application on Vietnamese vaccine articles (VRAG): part 1"
tags: ["rag", "vector-database", "vector-store", "vrag"]
categories: []
series: ["Vietnamese RAG"]
---

[The series](tags/vrag)
---

I have a [[lc_vaccine_articles_json|dataset]] of over 600 articles (ethically) sourced from a pharmacy retailer's [site](https://nhathuoclongchau.com.vn). With this example dataset, in this series, I show how to quickly prototype a baseline RAG.

## Refresher on RAG

![](https://developer-blogs.nvidia.com/wp-content/uploads/2023/12/rag-pipeline-ingest-query-flow-b-2048x960.png)
*(Source: [NVIDIA](https://developer.nvidia.com/blog/rag-101-demystifying-retrieval-augmented-generation-pipelines/))*

In a RAG application, we typically want to emphasize on optimizing data digestion and similarity search:
- **Digestion** is the way you chunk and embed, probably index, data. Good digestion can improve search accuracy and speed
- **Similarity search**: this goes hand in hand with digestion: usually, certain indexing is required for certain search algorithms.

In **Digestion**, we have the option to configure/implement chunking, even embedding ourselves. For storing, indexing, and **similarity search**, people typically use a pre-built [[vector_db|vector store]]

>Under the hood, Chroma DB used  [HNSW index](https://cookbook.chromadb.dev/core/concepts/#vector-index-hnsw-index) to index vectors. It seems to also used [relational databases as metadata storage and filtering](https://cookbook.chromadb.dev/core/concepts/#document-and-metadata-index). 
>Pinecone developed their own [graph algorithm](https://www.pinecone.io/blog/hnsw-not-enough/#The-Pinecone-Approach:-Purpose-built-for-Vector-Search)

## Embedding
 
 To embed, you need an embedding model. Here's my [[embedding_machine|blog]] on embedding for RAG



---
Sources:

Pinecone has several unmissable learning techblog [series](https://www.pinecone.io/learn/):
- RAG: https://www.pinecone.io/learn/series/rag/
- FAISS (Facebook AI Similarity Search): https://www.pinecone.io/learn/series/faiss/

