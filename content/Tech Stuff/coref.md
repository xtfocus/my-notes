---
title: Coreference resolution
tags: ["coref"]
---

In my articles on [[text2graph]] and [[LLM benchmarking|llm_benchmarking]], I emphasized how coref (shorts for Coreference Resolution) is important for sophisticated NLP applications, where co-occurence and relationships between entities matter. This also applies to RAG, as bolstered by a questionable redditor data scientist [here](https://www.reddit.com/r/LocalLLaMA/comments/18izvbh/comment/kdh1hof/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)

<p align="center">
  <img src="https://explosion.ai/blog//coref-1.gif" alt="coref_spacy.png"/>
</p>



In this article, I review the history of coref. Inspired by [this blog from explosion.ai](https://explosion.ai/blog/coref), I branch out and summarize important concepts.

## Benchmarks (evaluation)

To warmup, let's start with evaluation (because it's model-agnostic). There are many coref metrics, as can be seen [here](https://paperswithcode.com/task/coreference-resolution). Most popular ones are [OntoNotes](https://www.cs.cmu.edu/~hovy/papers/09OntoNotes-GALEbook.pdf), [WinoGrad](https://cdn.aaai.org/ocs/4492/4492-21843-1-PB.pdf) (both came out in 2011), [CoNLL-2012](https://aclanthology.org/W12-4501.pdf). More recent metrics include WikiCoref (2016), ACE (2005), etc.

Benchmarks come with their own dataset and challenges. The challenges can be divided into 2 types:
- Entity resolution: pronoun resolution is a sub-type of this

<p align="center">
  <img src="attachments/entity_res.png" alt="entity_res.png"/>
</p>
*(Example demonstrates common coref jargons. [Source](https://sentic.net/survey-on-coreference-resolution.pdf))*

- Event resolution:

 TO BE UPDATED

---
Resources:

- https://explosion.ai/blog/coref
- https://aclanthology.org/D17-1018/
- https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00300/43539/SpanBERT-Improving-Pre-training-by-Representing
- https://www.johnsnowlabs.com/coreference-resolution-with-bert-based-models/
- https://www.reddit.com/r/LocalLLaMA/comments/18izvbh/comment/kdh1hof/
- https://huggingface.co/biu-nlp/f-coref

- Recent survey papers: more recent metrics, annotation formats and guide, etc.
    - https://aclanthology.org/2021.crac-1.1.pdf
    - https://sentic.net/survey-on-coreference-resolution.pdf
