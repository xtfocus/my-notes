---
title: 📏LLM benchmarking
tags: ["benchmarking"]
categories: []
---

Beginner to the LLM field might get lost in the metrics spaghetti. And new benchmarks got published every once in a while.

<img src="attachments/openllm_leaderboard.png" alt="openllm_leaderboard.png" width="840" />

In this article, I introduce benchmarks used in the [Huggingface Open LLM leaderboard](https://huggingfaceh4-open-llm-leaderboard.hf.space/). For each benchmark, we cover:
- what do they test
- what's the data look like
- trivial info if any

## HellaSwag

#### It tests common-sense

[HellaSwag](https://arxiv.org/abs/1905.07830), short for *Harder Endings, Longer contexts, and Low-shot Activities for Situations With Adversarial Generations*, 19 May 2019, was a dataset in form of a cloze test to see if language models (LM) can finish a sentence. This task is called *common-sense natural language inference* (*common-sense NLI*)

>...given an event description
such as “A woman sits at a piano,” a machine
must select the most likely followup: “She
sets her fingers on the keys.”

🧐 **Trivial:** Similar to HellaSwag, [WINOGRANDE](https://arxiv.org/pdf/1907.10641), [Physical IQA](https://arxiv.org/abs/1911.11641) and [CosmosQA](https://arxiv.org/abs/1909.00277) also test common-sense (they all came out in 2019).

#### The data

HellaSwag official leaderboard, code and data here: https://rowanzellers.com/hellaswag/

The data covers a wide range of topics from WikiHow and manual can be found on the internet

>We scrape 80k context and follow-up paragraphs
from WikiHow, covering such diverse topics as
“how to make an origami owl” to “how to survive
a bank robbery.” Each context has at most three
sentences, as do the follow-ups.

<img src="attachments/dragonfly.png" alt="dragonfly.png"  width="520" />

Given these contexts, the author trained an algorithm that is very good at tricking LMs into picking the wrong answers over the correct one:

>We use Adversarial Filtering (AF), a data-
collection paradigm in which a series of discrim-
inators is used to select a challenging set of gen-
erated wrong answers.

💡The HellaSwag paper offered not just a dataset but the means of dataset creation, so that overtime we can create new dataset to challenge LMs.

>What happens when HellaSwag gets solved? We
believe the answer is simple: crowdsource another
dataset, with the same exact format, and see where
models fail. Indeed, in our work we found this to
be straightforward from an algorithmic perspec-
tive: by throwing in the best known generator
(GPT) and the best known discriminator (BERT-
Large), we made a dataset that is adversarial - not
just to BERT, but to all models we have access to.

>...the data source must be complex enough that state-
of-the-art generators often make mistakes, while
simple enough such that discriminators often fail
to catch them.

#### Setup

In the original paper, zero-shot evaluation was used, however it's more common these days to few-shot. The [Huggingface Open LLM leaderboard](https://huggingface.co/datasets/open-llm-leaderboard/results) used 10-shot HellaSwag, meaning for each HellaSwag question, the model will be provided with 10 demonstration examples with answers.

Scripts to run HellaSwag yourself is included on its [website](https://rowanzellers.com/hellaswag/)

## WINOGRANDE - another common-sense test

that is focused on the coreference resolution task.

https://arxiv.org/pdf/1907.10641

#### Dataset

on [Huggingface](https://huggingface.co/datasets/winogrande)

<img src="attachments/winogrande_twin.png" alt="winogrande_twin.png"  width="520" />

The dataset presents *twin sentences*: a pair of sentences that are just one word different from each other, but this word decides what is being refered to. In the examples above, **it** refers to the bottle in the first sentece. In the second sentence, the child is now full, he doesn't need the bottle anymore, so the toy's usefulness is relevant here.

Common-sense, reasoning and a good grasp of complex grammar structure are needed to perform well on this task.

The original dataset contains 44k pairs (likely increasing)

🧐 **Trivial:**  The paper brought up sharp observations and analysis about bias in the crowd-sourced data.

#### Setup

5-shot on the Huggingface Open LLM leaderboard.

## AI2 Reasoning Challenge (ARC)

[ARC](https://arxiv.org/abs/1803.05457), 2018, tests knowledge and reasoning. Before ARC, there was SQuAD and SNLI, but ARC branded itself to be more challenging.

#### Dataset

https://huggingface.co/datasets/allenai/ai2_arc

<img src="attachments/ai2arc_example.png" alt="ai2arc_example.png"  width="420" />

>The ARC Dataset consists of a collection of 7787 natural science questions, namely questions authored for use on standardized tests.

>non-diagram, multiple choice (typically 4-way multiple
choice). They are drawn from a variety of sources, and
sorted into a Challenge Set of 2590 "hard" questions ... and an Easy Set of 5197 questions

#### Setup

25-shot on the Huggingface Open LLM leaderboard. That's a lot of shots. At the momment, [GPT4 was leading this metric](https://paperswithcode.com/sota/common-sense-reasoning-on-arc-challenge)

## MMLU

(Code, leaderboard, and datasets: https://github.com/hendrycks/test)

#### It tests model's skills on specialized subjects

MMLU, 12 Jan 2021, stands for [*Massive Multitask Language Understanding*](https://arxiv.org/abs/2009.03300). MMLU brands itself as a test on a diverse set *specialized subjects*, which people must study to learn, contrast to common-sense which "almost every child has".

>To succeed at our test, future models should be well-rounded, possess extensive world knowledge, and develop expert-level problem solving ability

#### Dataset

All **57** specialized subjects (or *tasks*, termed by the author) are listed in page 15th of the paper. Some subjects include Algebra, US Foreign Policy, Virology, Professional Law, Medicine, and Psychology. The dataset is a result of crowdsourcing.

Similar to HellaSwag, MMLU follows the multiple-choice format

<img src="attachments/glue_fewshots.png" alt="glue_fewshots.png"  width="380" />

The MMLU paper is a really important one.


#### Setup

In the paper and on Huggingface Open LLM leaderboard, a fixed set of 5 few-shot examples is created for each subject. 

## TruthfulQA

#### It tests if model yields false information

Despite being trained on massive knowledge, LMs can generate very human-like, good looking answers that are incorrect. Why? It's a mystery but must have something to do with how models are trained:

>... the model’s training objec-
tive actually incentivizes a false answer. We call
such false answers *imitative falsehoods*. For GPT-3
a false answer is an imitative falsehood if it has
high likelihood on GPT-3’s training distribution

<img src="attachments/truthfulQA_gpt3.png" alt="truthfulQA_gpt3.png"  width="420" />

>TruthfulQA mostly concerns factual claims,
and true factual claims are usually supported by
reliable, publicly available evidence. Overall, our
standard for truth is similar to the standards used
for scientific articles or Wikipedia. 

🧐 **Trivial:**  Skeptics like me might see this definition of truthfulness problematic. What if Bigfoot is real? What if smashing mirrors actually give you 7 years of bad luck (lol, but seriously)

In my opinion, a more meaningful truthful benchmark should be one that is not only domain specific but also defined by the enterprise adopting it. For instance, look up the Air Canada Chatbot Incident.

Having said that, this paper laid some concrete thinking exercise on how to approach truthfulness benchmarking. For example, if the model says it doesn't know the answer, that still counts as being truthful.


#### Dataset

>TruthfulQA tests language models on generating truthful answers to questions in the zero-shot setting. It comprises 817 questions that span 38 categories. The benchmark and code is available at https://github.com/sylinrl/TruthfulQA


>Most questions are one-sentence long with a median length of 9 words. Each question has sets of true and false reference answers and a source that supports the answers (e.g. a Wikipedia page).


A better look at the dataset is on [Huggingface](https://huggingface.co/datasets/truthful_qa).

#### Setup

In the original paper, models perform 2 tasks (all in zero-shot):
- generation: the main task. Human will score truthfulness in the answer from 0 to 1.
- multiple-choice: the additional task

🧐 **Trivial:**  human evaluators are costly, so the author also used an AI (GPT-judge) to automate  the evaluation of the generation task. This model mastered the benchmarking questions and is able to score answers with ~90-96% validation accuracy. While this isn't great, it is an interesting idea that can be adapted to specific business use cases (e.g., an expert on Air Canada policy that can tell if GPT4 is giving customers non-existent flying deals)

🧐 **Trivial:**  The paper reported an alarming trend where larger LLMs are more informative but also less truthful.

Evaluation for multiple-choice should be straightforward. 

For the generation task, I believe the GPT-judge idea is actually used (diver goes [here](https://github.com/sylinrl/TruthfulQA/blob/fdd8ad1c0d00a478cf8b0bb41a3ad8378c16293b/truthfulqa/models.py)). Each generation question comes with several incorrect and correct example answers. This is likely to instruct the judge models. Other than that, the examples can also be used as reference for semantic similarity models like BLEURT or T5 implementations of BLEU and ROUGE (see their [metrics code](https://github.com/sylinrl/TruthfulQA/blob/fdd8ad1c0d00a478cf8b0bb41a3ad8378c16293b/truthfulqa/metrics.py)).

## GSM8k

2018, short for Grade School Math 8k, is a curated dataset of 8.5K math problems for evaluating the mathematical reasoning capabilities of LMs.

>These problems take between 2 and 8 steps to solve, and
solutions primarily involve performing a sequence of elementary calculations
using basic arithmetic operations (+ − ×÷) to reach the final answer. A bright
middle school student should be able to solve every problem.

🧐 **Trivial:**  The original proposed an automation method to evaluate LMs performance on GSM8k, namely a *verifier*, similar to GPT-judge in the TruthfulQA paper

#### Setup

In the original paper, LMs are fine-tuned on 7.5/8.5K examples before evaluation on other 1K examples.

On Huggingface Open LLM leaderboard, I believe models are not fine-tuned but rather 5-shot evaluated on GSM8k.


## Ending: it's not the end

There are many other benchmarks (not on [Huggingface Open LLM leaderboard](https://huggingfaceh4-open-llm-leaderboard.hf.space/)) :
- LAMBADA - predict missing word: https://paperswithcode.com/dataset/lambada
- [GLUE](https://gluebenchmark.com/) and [SuperGLUE](https://super.gluebenchmark.com/): general language understanding
- Benchmarking Code generation models: HumanEval, CodeXGLUE, etc
- In this [article](https://www.openphilanthropy.org/rfp-llm-benchmarks/), the author critique the benchmarks for their *self-contained* natural, as answers are known. This implies a lack of benchmarking on real life open-ended problems.


---
Resources:

- A Survey on Evaluation of Large Language Models https://arxiv.org/pdf/2307.03109
- Large Language Models: A Survey https://arxiv.org/html/2402.06196v2
- https://github.com/leobeeson/llm_benchmarks
- Discussion about the leaderboard on reddit [here](https://www.reddit.com/r/LocalLLaMA/comments/18hnzzn/a_107b_model_is_now_on_the_top_of_the_llm/) and [here](https://www.reddit.com/r/LocalLLaMA/comments/184nekd/is_open_llm_leaderboard_reliable_source_yi34b_is/)
