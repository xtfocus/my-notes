---
title: "Text to graph: Why and How "
tags: ["rag", "vector-database", "graph"]
---

From my experience, graphs are a powerful way to store knowledge. Graphing ability is the only reason I picked Quartz over Hugo for this site.

## Why graph
There are so many things to learn, and it's hard to navigate the huge amount of information coming out each day. I want to be able to store the information in a smarter way, with links and contexts:

- Links: Direct relationships between entities. For instance: 

```
"Mamba"-[is_a] -> "State Space Model"

```
Other than `is_a`: `compatible_with`, `is_framework_of`, etc.

- Contexts: a context is like an document (e.g, blogs, youtube videos, research papers) where nodes appear together. 

```
"Research Paper A"-[mentions]->"Deep Learning"
"Research Paper A"-[mentions]->"Convolutional Neural Networks"
```

Contexts can also be modeled as nodes. Context nodes create more paths in the graph, and this enriches the graph’s **semantic depth** and aids in multi-dimensional **analysis** and **complex query** ability.
## How

From my last working experience with text to graph, I came to the conclusion that a text to graph application has the following components:
- an entity generator: nodes have to come from somewhere. You either already had a comprehensive list of **entities of interest**, or you want to be able to add more entities to the graph as you go. Entities should be uniquely identified, categorized
- an entity resolutioner: takes care of synonyms
- a relationship extractor: Given known entities, parsing contexts and extract relationships
- a relationship refiner: relationship expressions can also have *synonyms*, or more accurately, paraphased

Obviously, the components above use NER among other NLP methods to extract entities and their relationships.

Entities have attributes, of which one of the most important one is probably time, or more specifically, the time it's created. This is a major headache.

The number of **relationship types** should be static to avoid running into overlapping/redundant edges. [Schema.org](https://schema.org/) is a catalog of common relationships that you can use in your graph.


## Case Study

Let's see how people did it.

### Simple Co-occurence Graph

https://towardsdatascience.com/nlp-and-graphs-go-hand-in-hand-with-neo4j-and-apoc-e57f59f46845

**Dataset**: https://www.kaggle.com/datasets/kevintoms/news-data

Each row is a piece of news, converted to a node with attributes: title and content

Use NER (third parties, outdated) to find people, organization, date, location, etc mentioned in content. Only one relationship type: `mentioned`

**Coreference Resolution** not applied

>Coreference resolution is the task of finding all expressions that refer to the same entity in a text. It is an important step for a lot of higher level NLP tasks that involve natural language understanding such as document summarization, question answering, and information extraction. ([nlp.standford.edu](https://nlp.stanford.edu/projects/coref.shtml))


This naive graph solution offers a baseline. Some improvements:

- Coreference resolution and even cross-documents coreference resolution 
- Better entity and relationship extraction
- Handle multiple relationship types
- A better structured way to import your predefined types/entities. Thinking Pydantic

In the next post, I implement all of these ideas.

---
Resource:
- [Better Handling Coreference Resolution in Aspect Level Sentiment Classification by Fine-Tuning Language Models](https://aclanthology.org/2023.crac-main.5.pdf)
- Coreference-Resolution before LLMs: https://unimatrixz.com/topics/ai-text/nlp-tasks/core-nlp-tasks/coreference-resolution/
- https://unimatrixz.com/topics/ai-text/nlp-tasks/reasoning/knowledge-and-information/knowledge-extraction-from-documents/

- Story as graph: https://unimatrixz.com/topics/ai-text/nlp-tasks/advanced-nlp-tasks/semantic-role-labeling/

- FastCoref
    - https://towardsdatascience.com/fastcoref-a-practical-package-for-coreference-resolution-bfbcb749e464
    - https://medium.com/@michaelwood33311/creating-accurate-ai-coreference-resolution-with-fastcoref-20f06044bdf9

- For the cowboys: https://towardsdatascience.com/how-to-convert-any-text-into-a-graph-of-concepts-110844f22a1a


