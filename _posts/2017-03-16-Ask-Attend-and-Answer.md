---
layout: post
author: xiaoyuliu
date: 2017-03-16 20:21
category: blog
title: "Ask, Attend and Answer: Exploring Question-Guided Spatial Attention for Visual Question Answering - Notes"
tag:
- vqa
- eccv 2016
---

### Related Work

0. Memory Network:

    - memory
    - four composents: input feature map, generalization, output feature map and response

1. Neural Attention Mechanism(soft):

    - Use an alignment function based on "*concatenation*" of the input and each candidate, see [this paper](https://arxiv.org/abs/1508.04025)
    - Use an alignment function based on the *dot product* of the input and each candidate, see [this paper](https://arxiv.org/abs/1503.08895)

    They both need to compute a scalar value according to input and candidate vector, and apply a softmax function to produce the attention weight for each candidate.

    In this paper, the author chosed the *dop product* alignment function because "*concatenation*" alignment function does not yield good performance on the (neural machine translation task)[https://arxiv.org/abs/1508.04025]. 

    <span class="evidence">Make sense?</span>

2. Compared Models:

    - ACK model: Feed the generated image caption and relevant external knowledge as input to a LSTM.
    - DPPnet model: Learn a CNN with some parameters predicted from a seperate network, which utilizes Gate Recurrent Unit to generate a quesition representation. Based on the input question, it maps the predicted weights to CNN needed to be learned via hashing.

    Neither of them contains a **spatial attention mechanism**, also they both use external knowledge. So, in this paper, the author is going to solve these two problems.


### Contribution

0. 

