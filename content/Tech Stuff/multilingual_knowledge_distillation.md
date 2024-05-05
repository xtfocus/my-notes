---
title: 🌐Multilingual Knowledge Distillation
tags: ["fine-tuning", "sentence-transformer"]
---

To train a sentence-transformer for, say, the Thai language, you need to:
- start with *pre-training*: something like Masked-language-modeling
- fine-tune to produce more complex sentence vectors. Similar to the *Next sentence Prediction* task in BERT, here people typically use labeled sentence pairs, in one way or another:
    - [Natural Language Inference](https://paperswithcode.com/task/natural-language-inference): labels include contradiction, neutral or entailment
    - [Semantic Textual Similarity](how similar two pieces of texts are): label is a similarity score, e.g., from 1 to 5. 

What if we don't have labeled sentence pairs in Thai but **translation pairs**? We can do [**Multilingual Knowledge Distillation**](https://arxiv.org/abs/2004.09813)

![](https://cdn.sanity.io/images/vr8gru94/production/8bf5491d704333a1678181c844041adf72bbe628-1920x840.png)
*(Source: [Pinecone](https://www.pinecone.io/learn/series/nlp/unsupervised-training-sentence-transformers/))*

Using English to Thai translation pairs (called the *parallel data*), we:
- input the English into both teacher and student models to generate vectors.
- input the Thai into the student model only
- optimize the student model by calculating the MSE loss between the teacher and student vectors.


---
Sources:
- Implementing Distillation: https://www.pinecone.io/learn/series/nlp/multilingual-transformers/

