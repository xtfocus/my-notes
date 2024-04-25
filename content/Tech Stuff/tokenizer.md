---
title: "Know your tokenizer"
tags: []
categories: []
series: [""]
---

![tokenizer_meme.png](attachments/tokenizer_meme.png)

Andrej Karpathy recently dopped a video on the GPT's tokenizers, which is the BPE tokenizer. He addressed many interesting points, one of which is that poor generation may stem from poor tokenization.

In this blog, I review major concepts in tokenization, in the interview manner.

**Q: Where is the tokenization in the big picture of language modeling**

A: Tokenizers take raw documents as input and output cleaned (stemmed, lemmatized, etc.) pieces of information called tokens. The tokens are then fed into ML algorithms. Tokenizers are an attempt to limit the vast range of human expression text data down to a fixed-length **vocabulary**. It's also ideal that each token in this vocabulary is actually meaningful.

---

**Q: Briefly describe some tokenization techniques, with the emphasize of modern subwords tokenizers**

A: You can see each character or each word as a token, with the latter being limited in coverage, since new words are coming out every day. Subwords tokenization algorithms were born to fix that: it uses characters but also **merge** them where it makes sense, resulting in new tokens. Some popular subwords tokenizers:

- BPE Algorithm: iteratively merge tokens based on high frequency

BPE was used in GPT models, RoBERTa, BART, etc.

- Unigram Algorithm: iteratively merge tokens based on probability. Therefore, less frequent token pairs also have some chance to be selected, and the most frequent also has the ability to be dropped. It's more flexible and less greedy than BPE, but the output can be random, due to its probabilistic nature.

It's used in AlBERT, T5, mBART, Big Bird, and XLNet.

- WordPiece: BPE fell hard for the high frequency, but may ignore a less frequent pair whose members are strongly associated (similar to the reason why we need Lift in association rules). WordPiece fixes this by using the following score to evaluate pairs:

```
score = (freq_of_pair) / (freq_of_first_element × freq_of_second_element)

```

WordPiece was used in BERT and many BERT-based models (except RoBERTa)

---
**Q: What's the components of a tokenizer**

A: Typically there are the following components

- Normalization: lowercase, remove excess spaces, accents ('hếllo --> hello', resumé --> resume), [Unicode Normalization](https://unicode.org/reports/tr15/) (make sure that *equivalent* strings have a unique binary representation), etc.
- Pre-tokenization: Splitting text into words
- Modeling: algorithms are applied here to learn a vocabulary
- Post-processing (adding the special tokens of the tokenizer, generating the attention mask and [token type IDs](https://huggingface.co/transformers/v3.2.0/glossary.html#token-type-ids), depends on the training setup)

It's not uncommon for a tokenizer to ommit some of the components above. For example, you can choose to skip Normalization

---
Sources:
- https://www.freecodecamp.org/news/train-algorithms-from-scratch-with-hugging-face/
- https://huggingface.co/learn/nlp-course/en/chapter6/6
- https://www.kaggle.com/code/xuntngnguyn/vietnamese-text-classification-like-youre-paid
