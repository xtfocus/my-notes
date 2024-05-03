---
title: "Text to graph: Why and How "
tags: ["rag", "vector-database", "graph"]
---

From my experience, graphs are a powerful way to store knowledge. Graphing ability is the only reason I picked Quartz over Hugo for this site.

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

