---
title: Guardrailing for LLMs
tags: ["rag", "llmops", "mlops"]
categories: []
---

Guardrailing, moderationo governance, etc., means making the LLM safe.

LLMs are best known for malfunction. Without proper "guidance", an LLM solution might get revoked & never see sunlight.

In this post, I review some concepts in LLM Guardrailing.

Ideally, you should guardrail both input and output.

# Input Guardrails

The goal is to prevent unwanted prompts from getting into the chain.

>Some types of unwanted prompts:
>
>- **Out-of-domain** queries
>- **Jailbreaking**: overwriting the system prompt. DAN-GPT is an example
>- **Promtp Injection**: inject some malicious code that can actually be executed somewhere down the chain.
>
>*Source: https://cookbook.openai.com/examples/how_to_use_guardrails*

## Prompt Classification

To prevent harms, you need to be able to detect them. So, **you need a prompt classifier**.

What you should do is to, based on the business rules, define your own **taxonomy of risks** - these become the classes of the classifier. 

<sub><sup>(If you are building a taxonomy and don't know where to start, take inspiration from the Llama Guard paper).</sup></sub>

How Llama Guard paper framed input guardraling is: given an input, for each category, output `safe` or `unsafe`. Essentially `n` binary classification problems. (`n` = number of risks)

## Safe Prompts only

Instead of telling LLMs what they are not allowed to do, you can tell them to stick to they are allowed to. How you do it: see Nvidia Nemo [paper](https://aclanthology.org/2023.emnlp-demo.40.pdf), and James Briggs [intuition + implementation tutorial](https://www.pinecone.io/learn/nemo-guardrails-intro/). 

This is a more conservative measure than prompt classification. You can use both, just like in Nemo.

# Output Guardrails

You also need to define a risk-taxonomy for LLM output. 

Types of unwanted outputs:
- **Out-of-domain reponse**: obviously
- **Unsafe**: output breaches at least one of the risk guidlines. Here you can re-use the guidlines for input-guardrailing.
- **Hallucination/fact-checking**: It's a complicated problem. For domain-specific applications, you can use a corpus of ground truth information. Some solutions use search engines (e.g., Amazon's Refchecker use Google search)
- Fairness: check for biased response


# What's next

In this post I hasn't discuss how these ideas are implemented, but you can find them in the resource section. Guardrailing tools include: heuristic rules, LLM-based and non-LLM-based classifiers, each comes with advantages and handicaps. 

Another concern is the trade-off between security and latency. A possible by-pass is to read-retrieve-generate and red-teaming at the same time upon the reception of a user request.

---

Resources:

- https://ai.meta.com/research/publications/llama-guard-llm-based-input-output-safeguard-for-human-ai-conversations/
- https://cookbook.openai.com/examples/how_to_use_guardrails
https://www.pinecone.io/learn/nemo-guardrails-intro/
- https://aclanthology.org/2023.emnlp-demo.40.pdf
- https://www.amazon.science/code-and-datasets/refchecker
- https://arxiv.org/html/2402.01822v1
- https://github.com/aurelio-labs/semantic-router
