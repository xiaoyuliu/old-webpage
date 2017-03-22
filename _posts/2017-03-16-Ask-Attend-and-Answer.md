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

### Contribution

0. Combine spatial attention in solving VQA problem, which allows to visualize the spatial inference process(acutally the attention part).
1. Two-hop structure: 
    0. In the first hop, aligns words with image patches; 
    1. Then the second hop considers the whole question to choose visual evidence from the results of the first hop.
2. Create a set of synthetic questions to prove the ability of SMer-VQA to learn logical inference rules by visualizing the attention weights.
3. Evaluate several existing models and compare with SMer-VQA on public datasets.


### Related Work

0. Memory Network:
    - This model is investigated in question answering, where the long-term memory acts as a dynamic knowledge base and the output is a textual response. It is composed of:
        - memory
        - four composents: input feature map, generalization, output feature map and response

1. Neural Attention Mechanism(soft):
    - Use an alignment function based on "*concatenation*" of the input and each candidate, see [this paper](https://arxiv.org/abs/1508.04025)
    - Use an alignment function based on the *dot product* of the input and each candidate, see [this paper](https://arxiv.org/abs/1503.08895)

    They both need to compute a scalar value according to input and candidate vector, and apply a softmax function to produce the attention weight for each candidate.

    In this paper, the author chosed the *dop product* alignment function because "*concatenation*" alignment function does not yield good performance on the [neural machine translation task](https://arxiv.org/abs/1508.04025). 

    <span class="evidence">Make sense? And what's the difference between **soft** attention mechanism and the other?</span>

    The paper didn't provide result using *concatenation* alignment function.

2. Compared Models:
    - ACK model: Feed the generated image caption and relevant external knowledge as input to a LSTM.
    - DPPnet model: Learn a CNN with some parameters predicted from a seperate network, which utilizes Gate Recurrent Unit to generate a quesition representation. Based on the input question, it maps the predicted weights to CNN needed to be learned via hashing.

    Neither of them contains a **spatial attention mechanism**, also they both use external knowledge. So, in this paper, the author is going to solve these two problems.


### SMem-VQA structure

![SMEM-VQA](https://cl.ly/3c0h1m1l1O1L/Image%202017-03-21%20at%202.18.51%20PM.png)
<figcaption class="caption">Fig.1 Spatial Memory Nework for Visual Question Answering(SMem-VQA)</figcaption>

To simply describe what the network does: 
    - First align words and the corresponding embedded visual features, find regions related to `basket` and `cat` using two different embeddings. 
    - Then generate the selected visual evidence vector $S_att$ based on spacial attention weights(related to `basket`) and evidence embedding(related to "cat"). Basically this step accumulates `cat` presence features at the `basket` location
    - Finally combine $S_att$ and the question embedding $Q$(obtained using BOW) to predict the answer. 

Two embeddings are used: **"attention"** embedding $W_A$, **"evidence"** embedding $W_E$. 

$W_A$ is for generating spacial _attention weights_ at each location using word-guided atttention process in (b). Each word vector will separately select a related region with the highest correlation value, which provide more fine-grained attention. 

$W_E$ is responsible for detecting the presence of semantic concepts or objects that can be used as the "evidence" of the answer. And the embedding results are multiplied with *attention weights* and summed over all locations to generate the visual evidence vector $S_att$.

So far, the One-Hop model has been completed. But in the first hop, **all the words are utilized separately and only local evidence is provided, a way to extract additional correlated visual features to the whole question is needed**. Then the author came up with Two-Hop model, which made use of $S_att$ and $Q$ generated in the previous hop to update the question vector. The question vector will take place of the individual word vectors in hop 1, and everything should be updated accordingly.

### Result

The author proved the ability of attention mechanism to learn spacial inference through experiments on a simple symthetic dataset like Fig.2.

![synthetic dataset](https://cl.ly/2J3h292n2R1X/Image%202017-03-21%20at%204.43.40%20PM.png)
<figcaption class="caption">Fig.2 Absolute position experiment on the Synthetic Dataset</figcaption>>

Through comparison to other models, there are some interesting conclusions:
    
- Deep features are much better!<div class="spoiler"><p>(This is apparently correct)</p></div>
- iBOWIMG model has no spatial information because it did mean pooling.
- SMem-VQA will fail while there are multiple objects belong to the same category.
- Spatial attention is valuable.



