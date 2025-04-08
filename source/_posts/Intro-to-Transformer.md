---
title: Intro to Transformer
date: 2023-12-04 22:05:36
tags: 
- Deep Learning
- Paper
categories: 
- Tech
- Intro
# top: 10
---
#### **Sequence to Sequence Models**
#### The Limitations:
- Limitations of **RNN**:
    - BottleNeck Problem: The intermidiate representation cannot encode information from all the input timesteps;
    - Vanishing Gradient Problem: Gradients are vanished with multiple stacked recurrent layers.
- Limitations of **Seq2Seq**:
    - Seq2Seq models normally have an encoder-decoder architecture, encoders is
<!--more-->
#### Attention in Seq2Seq example

- In the encoder-decoder RNN case, we define a score between the hidden state of the decoder and all the hidden states of the encoder.
- The resulte cauculated from the process above will be feed into a softmax to make the scores to be far from each other.

| Name | Score function |
| ---- | ---- |
| Content-base attention | $score(y_t, h_i) = cosine[y_t, h_i]$ |
| Additive | $score(y_t, h_i) = v_atanh(W_a[y_t; h_i])$ |
| Location-base | $a_{t,i} = softmax(W_ay_t)$ |
| General | $score(y_t, h_i) = y_t^TW_ah_i$ |
| Dot-product | $score(y_t, h_i) = y_t^Th_i$ |


#### **Transformer Encoder**
#### Positional Encoding
Positional Encoding is calculated using a fixed formula, and its values do not change. Each time data is received, it is simply added to the Positional Encoding matrix. 
In an Encoder, every character (or word) of a sentence is processed in parallel. When you convert a sequence into a set (tokenization), you lose the notion of order. 
Ideally, the positional encoding should satisfy the following criteria:
- It should output a unique encoding for each time-step (word’s position in a sentence)
- Distance between any two time-steps should be consistent across sentences with different lengths.
- Our model should generalize to longer sentences without any efforts. Its values should be bounded.
- It must be deterministic.

$$ PE_{pos,2i} = sin(\frac{pos}{10000^{2i/512}}) $$
$$ PE_{pos,2i+1} = cos(\frac{pos}{10000^{2i/512}}) $$

In the paper, the sine function tells the model to pay attention to a particular wavelength $\lambda$. 

#### Feature-based attention: the key, value, and query
Key-value-query concepts come from information retrieval systems. We use the keys to define the attention weights to look at the data and the values as the information that we will actually get.

<p align="center">
<img src="/img/transformer/qkv.png" width="80%" height="80%">
</p>

#### 

#### References ⭐
- [Transformer Architecture: The Positional Encoding](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/)
- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)
- [【Transformer】](https://zhuanlan.zhihu.com/p/403433120)