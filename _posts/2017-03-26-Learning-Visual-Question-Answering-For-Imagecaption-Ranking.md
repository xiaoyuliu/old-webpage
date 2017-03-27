---
layout: post
category: blog
author: xiaoyuliu
date: Sun 26 Mar 2017 02:54:53 PM PDT
title: "Leveraging Visual Question Answering for Image-Caption Ranking - Notes"
tag:
- VQA
- image caption ranking
- eccv 2016
---

This paper focused on ultilizing pre-trained VQA model as a **feature extraction** method to improve image caption ranking performance. The author **embeded** images and captions into the space of VQA questions and answers using VQA models.

### Image-Caption Ranking

0. **Formulation and Loss Function**

    Retrieve relevant images given a query caption, and relevant captions given a query image. When training, the input is ($$I,C$$), image and caption pair, with $$K-1$$ sample images and $$K-1$$ random captions, then the target for the network is 

    - **Retrieving $$I$$ from *K* captions given *C* **
    - **Retrieving $$C$$ from *K* images given *I* **

    The paper uses the sum of two expected negative log-likelihoods of image and caption retrieval over all image-caption pairs(*I,C*):

    $$
    L(\theta) = E_{(I,C)}[-logP_{im}(I|C)] + E_{(I,C)}[-logP_{cap}(C|I)]
    $$

1. **VQA-agnostic Models**

In VQA-agnostic models for image-caption ranking, there's no prior knowledge utilized from VQA corpora. They use a vectorized image representation, which is the hidden layer activation from an image classification pretrained CNN; and a vectorized caption representation, which is namely the encoded sentence using an RNN.

This kind of structure requires a high volume of training image-caption ranking dataset to learn the necessary knowledge. The state-of-the-art VQA-agnostic model for image-caption ranking task is also treated as (baseline)[https://arxiv.org/abs/1411.2539] in the paper.


