---
title: Introduce Llama-index DocumentSummaryIndex
tags: ["rag", "llmops"]
categories: []
---


(In the last 3 months I have been paid to work on production-grade chatbot projects. It's already September of 2024, time flies but chatbot applications are still in high demand. These 3 months gave me more quality insight on the current state of LLM applications)

In this article, I explain how to take advantage of of chunk's summary in Retrieval Augmented Generation applications. Despite the title, this article is more concerned about of implementation ideas, using Llama-index as an example.

# TLDR

In vanilla RAG, you have only the vector embedding of the full text as index, and you search on that index.

However, the full text migh be too long, sometimes too detail, sometimes in "LLM-unfriendly" tones. We just want to capture the essence. So instead of indexing chunks right away like in vanilla RAG, we can summarize them first. The summarization likely contains the essence with a LLM-friendly tones/styling, which possibly helps with retrieval accuracy as well.

Possible disadvantages:
- hallucination: LLM might introduce new details in summaries
- sacrifice of details: summarization might leave out important details, which harms retrieval recall.

Generating summaries also costs a lot of tokens, but you only have to do once if you do it correctly. Not a major disadvantage.


# Summary in index creation

A `DocumentSummaryIndex` is created with 3 steps:

1. Chunking: Documents are split into manageable chunks using **text-splitting** techniques of choice. (In Llama-index documents and codes, chunks are usually referred to as "nodes")
2. Summarization: After the document is broken into nodes (chunks), an **LLM generates a concise summary** for each chunk. This summary is stored and indexed
3. Embed summaries created in the previous step. This is optional, but is commonly applied, unless you plan **not to** use vector search during the retrieval stage.

Compared to the vanilla index, in `DocumentSummaryIndex`, the main difference is that we don't embed the whole text chunk, but summarize it first, then (optionally) embed it. What about the original full text? `DocumentSummaryIndex` stores them too, so that later when the chunk is retrieved, we also have access to the original context.

In the end, the index contains:
- full text chunks
- summaries of chunks
- embeddings of summaries

# Summary in retrieval

In vanilla RAG, we would perform vector search on the embeddings of **full text chunks**. With `DocumentSummaryIndex`, we perform vector search on the embeddings of **summaries** instead.

# Generation

Once retrieval is completed, i.e. relevant chunks are returned, we no longer need the summaries, since we have access to the full text chunks, which is more commonly used as context in the final prompt.

(Having said that, you have the ingredients, feel free to cook the way you want)

# Confusion

In Llama-index's [tutorial](https://docs.llamaindex.ai/en/stable/examples/index_structs/doc_summary/DocSummary/#build-document-summary-index) on `DocumentSummaryIndex`, you see the `response_synthesizer` being used in both retrieval and generation. Let's clarify its role.

First, the definition:
```python
response_synthesizer = get_response_synthesizer(
    response_mode="tree_summarize", use_async=True
)
```

`response_synthesizer` is nothing but a wrapper for prompting: It takes **chunks** and a **query** as input, and put those in a prompt template. The "tree_summarize" mode means instead of passing all chunks at once to the final prompt, and get the final answer from this huge context blob, we group chunks, get answers from groups, then group the answers, then get the answers out of these answers, and so on. See 
[this blogpost](https://www.bluelabellabs.com/blog/llamaindex-response-modes-explained/) for more detail. 

This clears up `response_synthesizer`'s role in the generation phase. How about in index creation?

During index creation, `response_synthesizer` only take in one chunk only, with the query as follow:

```python
DEFAULT_SUMMARY_QUERY = (
    "Describe what the provided text is about. "
    "Also describe some of the questions that this text can answer. "
)
```

To sum up, the naming of `tree_summarize` is confusing as hell. It should just be 'tree'.

Llama-index re-using the same `response_synthesizer` is efficient, but also caused me some confusion.

---
Resources:

[Llama-index notebook](https://docs.llamaindex.ai/en/stable/examples/index_structs/doc_summary/DocSummary/#high-level-querying)
[Llama-index source code](https://docs.llamaindex.ai/en/stable/api_reference/indices/document_summary/#llama_index.core.indices.DocumentSummaryIndex)
[About the response_synthesizer's response_mode](https://www.bluelabellabs.com/blog/llamaindex-response-modes-explained/)
[Linkedin-er fafoed and noted their understanding on this exact subject. The note is kinda messy](https://www.linkedin.com/pulse/comparison-document-summary-index-sentence-window-methods-zhang-bplae/)
[LLM in place of embedding for retrieval, how it works](https://www.llamaindex.ai/blog/using-llms-for-retrieval-and-reranking-23cf2d3a14b6)
