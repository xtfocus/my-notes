---
title: "Understanding perplexity"
tags: ["benchmarking"]
---

Perplexity is one of the common metrics for evaluating text generators.

It measures how uncertain the language model is about its output. Small perplexity scoring is favorable.

In this post: 
- Perplexity formula
- Calculate perplexity for a corpus in Python for GPT2: a decoder model

## The formula


<p align="center">
  <img src="attachments/perplexity_formula.png" alt="perplexity_formula"/>
</p>

>equivalent to the exponentiation of the cross-entropy between the data and model predictions
>*(Source: [Huggingface](https://huggingface.co/docs/transformers/perplexity))*

## Calculate perplexity for a corpus

Here we use [`gpt2-large`](https://huggingface.co/openai-community/gpt2-large) (the large version of GPT2) to illustrate the computation.

Perplexity should be computed at each timestep, then we can take the average.

<img src="https://cdn-lfs.huggingface.co/datasets/huggingface/documentation-images/8e4421a0b180d9a5b5d8edee4705da19775ce52f80d297623cbbd29bdc406c9d?response-content-disposition=inline%3B+filename*%3DUTF-8%27%27ppl_full.gif%3B+filename%3D%22ppl_full.gif%22%3B&response-content-type=image%2Fgif&Expires=1713753252&Policy=eyJTdGF0ZW1lbnQiOlt7IkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTcxMzc1MzI1Mn19LCJSZXNvdXJjZSI6Imh0dHBzOi8vY2RuLWxmcy5odWdnaW5nZmFjZS5jby9kYXRhc2V0cy9odWdnaW5nZmFjZS9kb2N1bWVudGF0aW9uLWltYWdlcy84ZTQ0MjFhMGIxODBkOWE1YjVkOGVkZWU0NzA1ZGExOTc3NWNlNTJmODBkMjk3NjIzY2JiZDI5YmRjNDA2YzlkP3Jlc3BvbnNlLWNvbnRlbnQtZGlzcG9zaXRpb249KiZyZXNwb25zZS1jb250ZW50LXR5cGU9KiJ9XX0_&Signature=FPJ-YipchsGlCBkNc8wCf01%7EyOScGY2dwzDv7vrROi8Ua4Bm5gjAaTMBQJG01NM8ZERWZPD7ZCTncjimRBON27FpA1N%7ECSuB5Q9Bee%7EVWcKLXNr6QtzMIyBlEHAUhH%7EYm7Khsm8tbLlmQ-kApwN9NBVoUt0Xv%7EIbTjv2i5pu5FCYKzSfey2KExnxKJEYRRxCBFLo7c5XZsaJITpo5MBgVq9POrcYT9oM-bs%7ETXIYgsBNd3T5XtXPfZu2Q6va3aEijz9awl8yngVuPAZUEH2EL64JsWLIFlKks2qMRHiJfBMAMGPBdsZPXNwErRhNWkq-km%7EHPgQGPFQriohdAK06xg__&Key-Pair-Id=KVTP0A1DKRTAX" alt="Ask your pdf gif"  width="420" />

However, GPT2 is a fixed-length model at `n_positions=1024`. Therefore we can only calculate perplexity upto 1024 timesteps at once.

>What does it means GPT2-large is a fixed-length model?
>That means the dimension of the position embeddings is fixed, 1024 for this model, during training. This suggests that you should keep inference length no more than 1024 tokens, since the model was not trained on what's beyond that limit.
>What are positional embeddings? Check Resources. For instance, LLaMa uses *Rotary Positional Embeddings*

So, to estimate the overall perplexity, we can calculate perplexity for a sliding-window with window size = 1024, at each time step, before taking average.

<img src="https://cdn-lfs.huggingface.co/datasets/huggingface/documentation-images/c85f2334f8f447a1dc314f697a27074f586a5f305b666472a3e7d9297297df15?response-content-disposition=inline%3B+filename*%3DUTF-8%27%27ppl_sliding.gif%3B+filename%3D%22ppl_sliding.gif%22%3B&response-content-type=image%2Fgif&Expires=1713753252&Policy=eyJTdGF0ZW1lbnQiOlt7IkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTcxMzc1MzI1Mn19LCJSZXNvdXJjZSI6Imh0dHBzOi8vY2RuLWxmcy5odWdnaW5nZmFjZS5jby9kYXRhc2V0cy9odWdnaW5nZmFjZS9kb2N1bWVudGF0aW9uLWltYWdlcy9jODVmMjMzNGY4ZjQ0N2ExZGMzMTRmNjk3YTI3MDc0ZjU4NmE1ZjMwNWI2NjY0NzJhM2U3ZDkyOTcyOTdkZjE1P3Jlc3BvbnNlLWNvbnRlbnQtZGlzcG9zaXRpb249KiZyZXNwb25zZS1jb250ZW50LXR5cGU9KiJ9XX0_&Signature=nnTw4%7EbZHsfE-OezVio867j0Utqij848JqrQkwb8fyO7rX-Wn2R0TzeTXISzrogpHmGfrvNSpHFScPHvDBAGuAPTEB57uvE4nRwTFEMAM0M26zsHCBjJkWOOvyVLsvAA5HLjdy-vxpnIAejfdE00sT%7EW1-epjhBgEn%7ECnvpoVqhUM3LKwDiicBADWMP4WSiLLu-%7EjrIsXL8KRKHoPWVisBTZgLnNThtOHDzUvx3bn9EuQBVQ-qNssdHtAhy0PutY46BwAfxuTI-YIKvdrS7GPWlyqt8Wch04kCMioJ32gt5vqWb8r9O5jgPsY%7EPAjNS0Sod99j58XLOlFy5tyjczzw__&Key-Pair-Id=KVTP0A1DKRTAX" alt="Ask your pdf gif"  width="420" />

Once you understand how to calculate for each window, looping is trivial. A tutorial is available [here](https://huggingface.co/docs/transformers/perplexity).

In this blog, we will dive in the calculation for one window.


```python
import torch
from tqdm import tqdm
from transformers import GPT2LMHeadModel, GPT2TokenizerFast
from torch.nn import CrossEntropyLoss

device = "cuda"
model_id = "openai-community/gpt2-large"
model = GPT2LMHeadModel.from_pretrained(model_id).to(device)
tokenizer = GPT2TokenizerFast.from_pretrained(model_id)

# fixed context length = 1024
print(model.config.n_positions)

text = "Hugging Face is a startup based in New York City and Paris"
encodings = tokenizer(text, return_tensors="pt")

# Forward pass, gets logits 
input_ids = encodings['input_ids'].to(device)
labels = input_ids.clone()

print(f"Input length = {len(input_ids)})
print(tokenizer.tokenize(text)) # print the tokens

with torch.no_grad():
    outputs = model(input_ids, labels=labels)

# Calculate perplexity
loss_func = CrossEntropyLoss()
pred = outputs.logits[0][:-1,:]
lab = input_ids[0][1:]


print("Perplexity = ", torch.exp(loss_func(pred, lab)))
```
Explanation:
- At each timestep, the model guesses the next token. This guess takes a shape of a tensor `[1, 50257]`, where 50257 is the vocabulary size of this model. Each entry is the logit for the corresponding token to be chosen for text generation.
- We have 13 tokens in total. We compute the prediction above at each timestep, except for the  last one since we won't evaluate that timestep (we don't know what comes after "Paris") (`pred = outputs.logits[0][:-1,:]`). So the shape of `pred` is [12, 50257]
- The labels for evaluation: are the actual tokens in the sequence. We don't pick the first one since we don't know what come before "Hugging" `lab = input_ids[0][1:]`. The shape of lab is [12].

- Use [`CrossEntropyLoss`](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html) to compute the negative log likelihood from `pred` and `lab`. The logits in `pred` will be somehow "normalized", e.g., softmax, to retrieve the probablities. Finally, only the probability of the correct token will be selected to calculate Cross Entropy Loss, disregard of the probablities of the wrong tokens. You can verify this yourself or look at the doc.

```python
print("Example of CrossEntropyLoss")
n_vocab = 4
n_positions = 3
loss = CrossEntropyLoss()

logits = torch.randn(n_positions, n_vocab)

lab = torch.tensor([1, 2, 3])
    
with torch.no_grad():
    output = loss(input, target)
    
print(f"Before manipulating logis, output={output}")

# Shuffle logits of some wrong tokens for the 2nd timestep
# Let's swap tokens 0 and 3

temp = input[1,0].clone() # second time step -> index 1
input[1,0] = input[1,3].clone()
input[1,3] = temp

print(f"After manipulating logis, output={output}") # Doesn't change
```

- Finally use exp to get perplexity.

That's all, I hope you are now less perplexed.

Note: in the hugginface tutorial on perplexity, to avoid double computing for tokens, they these tokens to -100 in the subsequent iterations, since -100 is the default value of `ignore_index` in `CrossEntropyLoss`

---
Resources:

- https://www.ai-contentlab.com/2023/03/position-embedding-detailed-explanation.html
- https://machinelearningmastery.com/a-gentle-introduction-to-positional-encoding-in-transformer-models-part-1/
- https://aman.ai/primers/ai/LLaMA/
- https://www.upstage.ai/feed/product/llm-evaluation-part1-benchmark-datasets
- https://huggingface.co/learn/nlp-course/en/chapter7/3
