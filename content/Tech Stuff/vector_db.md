---
title: 🏹🛢Vector Store Introduction
aliases:
tags:
  - vector-database
  - vector-store
  - ann
  - approximate-nearest-neighbors
  - rag
---

A vector store (a.k.a a vector database), is a database for vectors. It can impact your RAG application performance in three ways:
- speed 
- accuracy
- memory

Ideally you want a database that searchs fast with high accuracy, with low memory consumption.

In this blog, I introduce some key concepts in vector database, and cover their indexing and searching techniques

## Intuition and Jargons

Say you have an unstructured knowledgebase (of texts, images, unlike those pretty tables).

You also have a **query** (also unstructured, unlike vanilla SQL `SELECT`s), to which you want most accurate **results**, assuming the more similar the more accurate.

For example: 
- knowledgebase: a book about health topics
- query 1: "Does eating too much fries lead to cancer"
- expected result: "According to document xyz, high consumption of fries is not associated with cancer"

The way people solve this these days is to embed everything: the knowledgebase, the query; then do a similarity search. You can write software to do it. Luckily, many has already done so and research into optimizing vector stores with respect to:
- Searching: gotta be efficient (fast)
- Handle large, multi-dimension tensors
- Parallelization, distributed computing

In this blog, I focus on search optimization only

## Search Optimization and Indexing

To improve search speed, indexing is crucial.

Indexing essentially means organizing. For a simple example, you have to sort an array in advance if you want to perform binary search (which is fast), or make a trie if you want efficient pattern matching. By reorganizing (indexing) the data in advance, you improve the searching speed.

The structure you used to organize records is called an index. A **vector index**, hence, is a data structure used to organize vectors: could be just incremental numbering like in tables, or hash in hash tables, or tree nodes, etc. In the context of data engineering, people sometimes use the terms *index* and *database* interchangeably.

## Indexing Strategies

You can just jump to this brilliant [Pinecone tech blog](https://www.pinecone.io/learn/series/faiss/vector-indexes/)

To summary, here are some indexing strategies:
- Flat indexing: means no indexing at all, just leave the vectors as is. The similarity between the query vector and every other vector in the index (the vector store) is computed. This is **slow (linear complexity)**.
- Approximate nearest-neighbors (ann): reduce vector size or reduce search scope, includes:
    - Locality Sensitive Hashing (LSH) indexing: uses a hashing function that has hash similar vectors to the same output(s). For example: Golden Retriever and Mastiff are more likely to be hashed to the same hash cluster (name it the dog bucket) than Mastiff and Burmese Cat are. Then at query time, we also hash the query (says Beagle), likely to be hashed to the dog bucket 9 out of 10 times. We then simply compare the query against all vectors in the dog bucket and retrieve the most similar. 
    There are several choices for LSH function: the traditional such as shingling, MinHashing, and banding; more modern ones such as Random Projection. Pinecone shared many high-quality articles on these options.
    - Inverted File Index (IVF): Similar to LHS in the sense that it reduces search scope by clustering first, but not quite:
        - In LHS: each sample and each query will be hashed multiple times, so one sample can be mapped to multiple hash buckets (many-to-many relationship between samples and buckets). 
        - In IVF: it's clustering so one sample can only be mapped to one bucket, so the buckets are disjoint. To start, use KMeans to calculate clusters and centroids. Given a query, find the closest centroid, then search all samples in the corresponding cluster. Very simple. There are multiple implementations of IVF, most include cluster-probing for edge cases; [some even uses quantization](https://aclanthology.org/2023.emnlp-main.116.pdf). 
    - Hierarchical Navigable Small Worlds (HNSW): top-tier performance for ANN ([source](https://github.com/erikbern/ann-benchmarks)), at the cost of the index's size (consume more memory). It's graph-based, and can be roughly seen as hierarchical clustering but there are also similarities between same-level-clusters. Traversing is from high to lower level, and from node to node.

<p align="center">
  <img src="attachments/HNSW.png" alt="HNSW.png"/>
</p>

>Most vector indexing/searching techniques are also applicable to **data deduplication**. Check [LSH](https://yorko.github.io/2023/practical-near-dup-detection/), [Inverted Indexing](https://users.cecs.anu.edu.au/~Peter.Christen/publications/ramadan2013dmapps.pdf), [HNSW](https://github.com/ncn-foreigners/blocking)

## Comparison

According to an experiment by a medium writer [here post](https://medium.com/@stepkurniawan/comparing-faiss-with-chroma-vector-stores-0953e1e619eb), FAISS is superior over ChromaDB in **context precision**, **context recall**, and search time. Here's their explanation:
>**Why is there discrepancy between vector stores?**
>The non-deterministic nature of Chroma’s algorithm for similarity search is attributed to its use of the approximate nearest neighbor (ANN) algorithm, specifically Hierarchical Navigable Small World (HNSW). During the search process, it involves the exclusion of some data points.
>Contrastingly, FAISS employs two components: (1) [Product quantization](https://inria.hal.science/inria-00514462/document) (PQ) encoding and (2) a search system with inverted indexing [1]. PQ introduces techniques for compressing high-dimensional vectors with a lossy approach. In the current implementation, it also uses HNSW as the search system. But, unlike the standard HNSW, this combination enhances search efficiency, resulting in faster search and more relevant contexts to the query.


---
Sources:
- https://aclanthology.org/2023.emnlp-main.116.pdf
- https://cornelltech.github.io/CS5112-F18/Lectures/Lec18.LSH.pdf
- https://www.datastax.com/guides/what-is-a-vector-index
- https://www.iunera.com/kraken/fabric/locality-sensitive-hashing-lsh/#1-how-does-lsh-work
- https://www.pinecone.io/learn/series/faiss/hnsw/
- https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing-random-projection/
- https://www.pinecone.io/learn/series/faiss/locality-sensitive-hashing/
- https://www.pinecone.io/learn/series/faiss/vector-indexes/
- https://www.uber.com/en-VN/blog/lsh/
