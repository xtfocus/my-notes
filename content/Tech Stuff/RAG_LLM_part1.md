---
title: {Build a RAG application on Vietnamese vaccine articles}
tags: []
categories: []
series: ["Vietnamese RAG"]
---

I have a datasets of 500 articles ethically sourced from a pharmacy retailer's [site](nhathuoclongchau.com.vn).

In this series, I show how you can prototype a baseline RAG fast.

## Refresher on RAG

![](https://developer-blogs.nvidia.com/wp-content/uploads/2023/12/rag-pipeline-ingest-query-flow-b-2048x960.png)
*(Source: [NVIDIA](https://developer.nvidia.com/blog/rag-101-demystifying-retrieval-augmented-generation-pipelines/))*

In a RAG application, we typically want to emphasize on optimizing data digestion and similarity search:
- Digestion is the way you chunk and embed, probably index, data. Good digestion can improve search accuracy and speed
- Similarity search: this goes hand in hand with digestion: usually, certain indexing is required for certain search algorithms.

We have the option to configure chunking, even embedding ourselves.

For storing, indexing, similarity search, we typically use a pre-built [[vector_db|vector store]] (some vector databases, e.g. [Chroma](https://docs.trychroma.com/embeddings), also offer embeddingout of the box). It helps to know what's going on under the hood, in case you want to fork it and create your own vector database.

>Under the hood, Chroma DB used  [HNSW index](https://cookbook.chromadb.dev/core/concepts/#vector-index-hnsw-index) to index vectors. It seems to also used [relational databases as metadata storage and filtering](https://cookbook.chromadb.dev/core/concepts/#document-and-metadata-index). 

---
Sources:
- https://www.pinecone.io/learn/series/faiss/hnsw/
